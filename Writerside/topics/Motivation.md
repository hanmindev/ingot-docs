# Why Ingot?

The creation of the mcfunction language is quite... unique. What was a set of console commands for players to control their game environment quickly evolved into its own programming language, albeit with many asterisks.

Since this was never really meant to be a proper programming language, but rather had features added to it until it became Turing complete, it is missing a lot of features common in other non-machine level languages, such as expression evaluation or proper if-else statements.

This makes writing mcfunction incredibly tedious, costing a lot of time and effort to write even simple programs. What could be written in a few lines in other languages could take hundreds of lines in mcfunction.

## How is this different from other languages which compile to mcfunction?

There have been numerous other mcfunction compiler projects for various purposes, however they seem to generally fall into two categories:

1. They are very similar to mcfunction, but provide some additional features such as expression evaluation or if-else statements, providing a more pleasant development experience with minimal performance loss.
2. They are very different from mcfunction, providing a higher level language which is easier to use, but runs slowly.

Having complicated projects in mind which would benefit from modern programming language features, I decided to approach this problem from a different angle.

A lot of inspiration regarding the [Rust programming language's](https://www.rust-lang.org/) approach to zero-cost abstractions was taken, in order to provide an easy to use yet performant language.

## Considerations

.mcfunction has a lot of weird quirks and limitations, which makes it difficult to write a compiler for it. A lot of the design decisions in other lnaguages do not apply here, and a lot of new considerations must be made.

There are many differences between mcfunction and other languages. To name a few:

1. Unlike other high-level languages, .mcfunction virtually has no concept of scope. All variables are global. This wouldn't really be an issue if it wasn't for the next difference:
2. Unlike what one would expect in modern hardware, data access inside Minecraft with mcfunction is incredibly slow. While the time it takes to move data from a register to memory, and vice versa is negligible today, it is not the case in .mcfunction. 
   * Thus, the stack should ideally be left alone unless the program absolutely cannot function without writing something to the stack.
   * To make matters worse, dynamic access is several times slower than normal access, making accessing arrays, or heap memory, incredibly slow. Thus, this should be seldom used.
3. Anything dynamic is incredibly slow. We touched upon dynamic memory access, but this also applies to dynamic function calls, dynamic IO, etc. For instance, while rotating an entity by a fixed amount is fast, rotating an entity by a variable amount can be several times slower. Thus, the developer should be aware of this.
4. Similar to functional programming languages, loops must be written in a recursive manner. Unlike any other language, many other constructs must also be written using functions, such as if-else statements.
5. .mcfunction has a concept of execution context which is not present in other languages but is incredibly powerful and should be leveraged as much as possible. Thus, the language should be designed to make it easy to use this feature.
   * Note: this feature is powerful not because it allows .mcfunction to have features missing in other languages; any sensible game engine should have a better way to do this. Rather, it is powerful because Minecraft gives us so little to work with.
6. Minecraft has access to most of the Java primitives such as signed 32-bit integers, floats, doubles, strings, etc, but many of them have weird restrictions on them.
   * Only integers can be stored in scoreboards, meaning that all other data types must be stored in storage which has a much slower access time.
   * Arithmetic operations can only be done on integers, and not floats or doubles. There are ways to get around this, such as casting to an integer, emulating floating point operations, etc., but they are all incredibly slow and the developer should be aware of this.

Ingot attempts to force the user to actively think about these considerations, as to not accidentally write slow code.