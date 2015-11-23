# Preliminary evaluation

## Introduction
I have not done nearly as much for this project as I should have by now. I have gone around in circles on how it should work, and I have been fighting scope creep back and forth. The main problem has been that I want to put in all the design effort that the language would need if it were to be a real, finished product; I have a hard time scaling it back to a half-semester project for a single course. Paradoxically, I also do not have this much infomation on the design; I kept trying to work on whatever product is due in a given week instead, and therefore did not even accomplish much overelaborate designing. However, I do have some accomplishments, which are summarized below.

## What I’ve done so far

I have a very good model of most of what the program does when it runs, and I should be able to hammer out the rest of the details easily. What remains is mostly figuring out a graph algorithm for how the computation proceeds, and I expect that I could find one that already exists somewhere. If I cannot find or invent a graph algorithm that works, I can come up with an extremely wasteful but working alternate method.

I mentioned most of my initial trouble in the introduction: overplanning at the expense of doing. I am still working on overcoming this problem in general, but in the specific case I believe I have mostly worked past that problem.

## What I have left to do

### Regular goals

I still have all of the coding left. I also have to write an exact grammar and parser, and choose some more minor details such as when to interpret types and how to pass things to shell scripts.

### Stretch goals

I doubt I’ll do any of these things; this section exists more because I feel like they are necessary parts of the finished product but that I will spend too much time working on them if I make them regular goals. They are implemented in approximate order of implementation probability.

* Module system: calling one of these programs from another.
* Ahead-of-time compilation
* Testing on multiple system types (Windows, etc.). In theory the program should work perfectly well on any system Python runs on; in practice I doubt that will happen without further testing.

## Conclusion

I have spent a lot of time over the past few weeks failing to work effectively on this project. I believe I have removed almost all my mental roadblocks for this project, and the rest are fairly well-defined; I should be able to get past them quickly. At that point, I will work on this project fairly effectively.
