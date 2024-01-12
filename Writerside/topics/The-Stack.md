# The Stack

## Why can't we just use the stack to hold function frames like normal systems?

In normal systems, local variables are stored on the stack, and when you call a function, the new variables are put on the top of the stack. When the function returns, the variables are popped off the stack.

In Minecraft although we can do the same thing, indexing the stack is a bit expensive just because Minecraft commands are poorly optimized. On top of this, there are a few other nuances, but the point is that the stack is not as efficient as it is in a normal system, and thus a stack is only used in datapack making when absolutely necessary.

## The Solution

An easy solution is to delegate the role of the stack to global memory. At compile time, we dedicate enough global memory for each function to store all of its local variables. If we assume that functions cannot call themselves recursively (even indirectly through other functions), we can emulate function stack frames without the inefficiencies of using a stack.

I'm sure this seems like an awful idea; we are pre-allocating a ton of memory for each function, but as far as runtime performance goes, it is the best solution.

This may seem wasteful, but it should not be too bad. There are some optimizations to reduce the number of scoreboards:

1. If two functions cannot be on the call stack at the same time, they can share the same scoreboards
2. A function only needs to store the scoreboards of the variables that are used after calling another function. If a function does not use any variable after calling another function, or does not call another function, then it can use only shared, temporary registers.

### Recursion

In our potential solution, we assumed that functions cannot call themselves recursively. Unfortunately, recursive functions are useful, so we need to find a way to support them.

The solution is to use a stack, but force the programmer to explicitly tell the compiler that they want to use a stack, so they are aware of the performance implications.

By using the `rec` keyword in front of a function, the programmer can tell the compiler that they want to use a stack.

Note that the `rec` keyword is only needed for functions that contain a cycle, and not necessarily by every function that a `rec` function calls.

Then, every time a `rec` function is called, additional memory will be allocated on the stack for the function to use. Note that using macros, we can allow the function to use the stack values directly, unlike in a normal system where the function would need to copy the stack values to registers.