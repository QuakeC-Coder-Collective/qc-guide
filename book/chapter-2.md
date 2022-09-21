<!-- Enable word-wrap -->

# Chapter 2: Anatomy of a Mod

## Source Files

QuakeC source files are the human-readable and editable files which a *compiler* (such as `fteqcc`) can convert into a format that can be interpreted by the Quake engine.  The compiler reads in a `progs.src` file to find what `.qc` files need to be compiled and in what order, as well as where to write the resulting output, `progs.dat`, which is what the engine will look for to run your mod.

Open the `progs.src` file from your `qc-106-src` folder.  Inside, you should see several lines beginning with `//` followed by the line `../progs.dat` followed by list of `.qc` source files.  The text after the `//` characters are called comments, and are ignored by the compiler.  The first *significant* (non-comment) line is a file path indicating the name and location of the output of compilation.  The `..` indicates that the output should be placed in the folder *above* the folder containing `progs.src`.  Further lines list all the files that are to be included for compilation.   The order of these lines is significant, as code fragments must be *declared* before they can be used by source files listed later in the ordering.

## Globals, Fields, and Built-ins

TBD

## User-Defined Functions

TBD
