## Concrete Syntax
Not fully decided. I know what I mean but I can’t yet put it into words.

## Parsed IR

There is a module. For now there is only one.

Each module has a dictionary of labeled outputs, a dictionary of labeled inputs, and a set of rules. The entries in these dictionaries can be nonexistant, or can be overridden by the commandline or later modules; any calculated object not in either dictionary is not available as in input or an output.

A rule has a dictionary of labeled input arguments, a command, and a dictionary of labeled output arguments. The command can be a shell script (with variable substitution not fully defined yet), another module (not yet), or a builtin command.

If given, the inputs to a module must be values (URLs, files (represented by names), or sets of either). The outputs must be file or directory names. 

The inputs and outputs to rules follow the same requirements as for modules, but can additionally be variable names. Additionally, inputs to rules can be labeled `foreach`, indicating that a set will be provided and the rule should be run for each element of the set. In this case, the Cartesian product of all the foreach rules is used, and all of the output arguments used must be sets.

Each value has an associated type from the ones mentioned above. Whetehr these types are runtime or compiletime remains to be seen.

## Executable IR

The same, except the labels are irrelevant (maybe?) and the set of rules has become a tree.

## Phases of translation

1.	Parse input file into rules
	* Other grammar elements will be supported later, but they will be interpreted statically.
	* If the input file *itself* changes, then the entire thing will reload? Not supported at first
2.	Look at labelled outputs
	* Outputs can be labelled in file
	* Outputs can be labelled on the command line
	* Latter take precedence
3.	Work backwards through ruleset; construct directed maybe-cyclic graph of rules.
	* Note which rules use labelled input for later
	* If any rule requires unbuilt input, throw error and halt
	* If any (used) rule’s definition requires an input it is not given, throw error and halt.
	* If types implemented, make sure they match.
4.	Sanity checks
	* If any rule (in main module?) is unused, throw warning
	* If further unused-rule warnings are enabled, error-check those rules as though they were used but demote errors to warnings
	* If there are no rules where the only inputs are sets (which default to empty) or provided (either to the rule or the module), throw error and halt.
	* If more than one rule outputs to a single non-set variable, give a warning. This means that there might be multiple fixpoints of the build process, and we don’t know which we’ll choose. TODO: Update this check based on build algorithm chosen below; there might be a better way.
5.	Find the set of all rules where all inputs are provided or sets (which can default to empty). These are the runnable rules.

## Phases of Execution (Currently taking place right after translation, instead of a program being saved)

Note that the graph is pruned at this point; only the rules which matter for the final outputs are listed.

1.	If, at runtime, the set of outputs is smaller than the compiled set, prune the tree further. This step is not necessary for the initial implementation because there is no advance compilation.
2.	For each runnable rule:
	1.	Check if the inputs have changed. If not, mark the rule as run and go to the next rule.
	2.	Check if we have exceeded the recursion limit of the rule. If so, abort with error.
	3.	Record the current state of all outputs to the rule.
	4.	Run the rule.
	5.	See if any outputs have changed. For each one that has, mark all the rules that depend on that output as in need of running.

TODO: Figure out how to work with the “runnable” and “in need of running” sets in a reasonable way, keeping in mind that the graph is not necessarily acyclic.
