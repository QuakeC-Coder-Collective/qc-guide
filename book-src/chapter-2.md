<!-- Enable word-wrap -->

# Chapter 2: Anatomy of a Mod

## Source Files

QuakeC source files are the human-readable and editable files which a *compiler* (such as `fteqcc`) can convert into a format that can be interpreted by the Quake engine.  The compiler reads in a `progs.src` file to find what `.qc` files need to be compiled and in what order, as well as where to write the resulting output, `progs.dat`, which is what the engine will look for to run your mod.

Open the `progs.src` file from your `qc-106-src` folder.  Inside, you should see several lines beginning with `//` followed by the line `../progs.dat` followed by list of `.qc` source files.  The text after the `//` characters are called comments, and are ignored by the compiler.  The first *significant* (non-comment) line is a file path indicating the name and location of the output of compilation.  The `..` indicates that the output should be placed in the folder *above* the folder containing `progs.src`.  Further lines list all the files that are to be included for compilation.   The order of these lines is significant, as code fragments must be *declared* before they can be used by source files listed later in the ordering.

## Variables

QuakeC files contain a list of declarations that assign names to different elements of code so that they can be referenced later.  There are essentially 3 different kinds declaration at the top level of a source file: variables, fields, and functions.  There are additional kinds of declarations added by fteqcc, but that's outside the scope of this section. 

Variables store bits of data that can be recalled at a later time.  Variables declared at the top level of a file (i.e. not contained in curly braces) are called *global variables* or *globals* for short.  Globals are different from other variables in that they can be accessed from any part of the code.  Variable declarations look like the following:
```
<TYPE> <NAME> [= <VALUE>];
```
e.g.:
```
float myVar = 13.7;

entity buddy;
```
The type indicates how the data held in the variable is interpreted and restricts how it may be used (it is not possible to divide an entity by 3, nor is it possible to obtain the health of a number).  The name identifies the variable so that we may specify it at another place to read from or write to.  Optionally, the varaible may be given an initial value by using the equals sign; without specifying the value, the variable is given a default value depending on its type.  Lastly, the declaration is closed with a semicolon.

A variable may be given one of several different types:

### float

A `float` is a number.  It may be negative, positive, or zero, and it may be fractional or whole.  The (admittedly obtuse) term "float" is short for "floating-point number" which refers to the fact that the number varies in precision, becoming less precise the farther the number gets from zero.  For most of our purposes the precision will be "good enough", although it's important to note that not all numbers can be represented, and that mathematical operations aren't always exact.  We'll discuss this later when we cover arithmetic.

The default value for a float is `0`.

### vector

A `vector` is an ordered bundle of three `float`s.  A vector might represent a location in space, a change in location in space (aka *displacement*), velocity, force, orientation (angles for pitch, yaw, and roll), etc.; or it might just be a collection of three unrelated values.  Vector *literals* (values as they are written out in source code, e.g. `3` is a float literal) are three numbers wrapped in single quotes: `'1 2 3'`.  Declaring a vector also creates 3 "hidden" declarations each ending with `_x`, `_y`, and `_z` to access each of the components seperately; that is, if we declare a vector `myCoolVector` we automatically get 3 `float`s named `myCoolVector_x`, `myCoolVector_y`, and `myCoolVector_z`.

The default value for a vector is `'0 0 0'`.

### string

A `string`, short for "string of characters", is a fragment of text.  String *literals* are written between two *double* quotes: `string hi = "Hello";` declares a string variable storing the word *Hello*.  Strings are used to communicate messages to the player and identify entities by target and class name, among other uses.

One important facet of strings is that they default to *null*, which is distinct from the empty string, `""`.  The implications of this distinction are covered later.

### entity

An `entity` is any game object, decoration, logical component (counters & relays), etc that exists inside the game world, *including the world itself*.  Every entity has several attributes called *fields*.  Each entity has the same fields as any other entity, although not all entities use every field, e.g. the monsters have AI routines stored in their `th_stand`, `th_walk`, `th_run`, etc. fields which are not found in other entities.

Entities default to the same entity as the world.  Actually, the entity named `world` is simply declared, and is never assigned a different entity by the engine at any time.

## Fields

Fields are like variables that are attached to entities.  Unlike variables, declaring a field doesn't directly create a place in memory for data to be stored.  Rather, when an entity is created, it will have a place in memory for each of its fields.  A field declaration looks like variable declaration, except that it starts with a `.` and it may *not* be given an initial value:
```
.<TYPE> <NAME>;
```
Since every entity contains space for every field, it's important to keep the number of declared fields in check.  The memory footprint of the entities in a map is (roughly) = the number of entities * the number of fields.  While the memory constraints on contemporary systems and source ports has grown, so have the sizes of maps and monster counts, generating even *more* entities from projectiles.

Fields may be assigned any type, including function types, which will be discussed next.

## Functions

Functions are fragments of code, which can be executed or *called* during various points in a program's lifetime.  Data may be sent to, or *passed* to the function before execution, and the function may have data sent back or *returned* to the code executing the function.  A function might look like the following:
```
float(float a, float b) add = {
    return a + b;
};
```
This declares a function named `add` with two *parameters*, `a` and `b` each with the type `float`, and a return type of `float`.  The function also has a *body* which contains 0 or more *statements* wrapped in curly braces.  The syntax for statements will be covered in more detail later, but just so you know, this statement finds the sum of values in `a` and `b` and returns that value.

What if we want a function that doesn't return a value?  For that, we use a special return type called `void`.  Here is a function that does *absolutely nothing* when we call it:
```
void() do_nothing = {};
```
While such a function *seems* useless, there are a number of places where it comes in handy.  In fact, there's a function `SUB_Null` declared like this in `subs.qc`.  They are useful when used as *callbacks*, which are functions stored or passed elsewhere to be called later.  We might have a callback perform an actual operation at one point, then store `SUB_Null` in the callback when we want to disable the operation.  We'll take a look at callbacks when we cover writing an actual entity.

Functions can also be declared *without* a body, then have its body declared later on.  We call these *forward declarations*.  A forward declaration by be used in the following way:
```
float() f;

float() g = {
    return f();
};

float() f = {
    return 12.9;
};
```
This allows us to use function `f` in function `g` *before* `f`'s body is defined.  While this particular example is contrived (we may as well have declared `f` with a body before `g`) it is sometimes useful to forward-declare a function that is located in a different file, so that you don't need care which files come in what order in `progs.src`.
