# Language design and implementation overview

The language is a declarative language similar to build systems like [`make`](http://pubs.opengroup.org/onlinepubs/9699919799/utilities/make.html#top), [CMake](https://cmake.org/), [`tup`](http://gittup.org/tup), etc. However, unlike build systems which take
files on the local computer and use them to make other files on the local computer in order to build software, this language is focussed on taking the files from the Internet, and making files on the local computer which are processed from common Internet files.

## Language design

### What constitutes a program

The user writes files in this language as structured text files, as with most programming languages. It is not intended to be used interactively (like, e.g., shell and Python), but instead it is expected that the entire program will be read in before it is run. I have not decided on whether or not source code will be compiled or directly executed, but I am leaning towards the latter model.

Each program will have as its input the contents current directory it is run in (or potentially another specified on the command line), and any Web resources it accesses. As output, it will create files and/or directories within its current directory. The expected behavior is that almost all files will at some point originate on the Web, but that there might also be local copies cached; I list the current directory as an input because the program might use these cached copies in the same way that `make` uses the intermediate files it previously built if possible.

For the purposes of this project, programs are to be written in standard text editors. I have no intention of providing so much as a syntax highlighting mode for Sublime. Depending on the quality of the products produced during this project, I may maintain the language later and add these features, but those are outside the scope of this class project.

### Standard operation

The DSL does not exactly have control structures because it does not have a standard definition of control flow. Instead, it has a list of actions to run which it builds into a dependency graph (again like build systems). Each action can have inputs and outputs, and if none of the inputs change then the outputs are not recomputed. However, there are three main differences between this and standard build systems:

1. The inputs can be web pages instead of files. This is the primary reason for making this project.
2. Inputs can be sets of web pages or files, not just single ones. This is because you might want to do the same thing to many different webpages, such as strip out site headers or combine them all into an ebook. Many build systems have something similar, like `make`’s implicit rules which allow you to easily turn a set of `.c` files into a single executable, but as far as I know none of them generalize well to URLs. _*I intend to do more research to see if any existing build systems have something I can adapt.*_ _*Potential change from original design*_: I originally wanted sets of files to be individual directories. I am now not sure if this is best, but it might still be.
3. A single rule can have the same set as both input and output. This means that this rule will be run repeatedly until nothing more is added to the set and none of the known set members change. This is useful for recursively downloading many links: if you want everything linked to by a page, and everything linked to by those pages, etc., then you can set a rule with the same URL group as both input and output in order to add each new link to the URL group. Mutual recursion and maximum recursion depth are also allowed but not yet fully designed.

Other than files, filesets, webpages, and webpage sets, the program might also support some common Web types like XPaths, CSS paths, or MIME types. Which if any of these is supported depends on what the built-in commands are and whether it makes sense to just treat all of them as strings.

### Error Handling

Sometimes, things don’t go according to plan. In some cases, this is completely irrecoverable: a parse error, for example, will force the program to quit right away. Assuming the program is read in correctly, it might still fail if it cannot download webpages, subcommands fail, etc. In this case, there are two main things the program does (or doesn’t do):

1. It reports the error (to the standard error stream), in as much detail as relevant (controllable with verbosity flags).
2. It *does not* change the outputs of the current action, and continues execution (although there may be a way to get abort-on-error functionality). For example, instead of saving the 404 page of a website, the program will act as though the webpage is completely unchanged; instead of creating a blank file when some potprocessing fails, the file will remain the same. This is important because in many cases the final outputs of the program are still usable in their old states, but would not be usable if the program were aborted halfway through. As an example, imagine that part of the program is supposed to download the images on a webpage, but instead fails. In this case, a likely cause is that the images URLs are dead, and the rest of the page is still partially useable without the images.

## Language implementation

I have been spending my effort on design instead of implementation for now, because designs are easier to change. As such, I cannot fully answer the questions posed. However, I have made some choices. First of all, this is going to be an internal DSL. This makes it easier to learn and use for people who are unfamilliar with the host language, and makes it easier to support whatever syntax I want. I also would not really get the most of the benefits of an internal DSL, because I don’t really need to integrate with any other language particular. The exception would be shell scripting, which I probably do need to integrate with for processing files, but it is very difficult to make an internal DSL in shell.

I have not decided on the syntax I do want to use, but it will probably be similar to some existing syntax for a build system; I intend to look at several build systems and see if any have syntaxes I can borrow. Instead, I have been focussing on semantics; I feel like the syntax should follow the semantics in this case. Once I have the semantics more fully specified, I will better be able to choose a syntax that matches.

