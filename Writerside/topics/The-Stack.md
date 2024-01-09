# The Stack

If these are similar, why can't we just do what normal systems do and utilize the stack?

In Minecraft although we can do the same thing, indexing the stack is a bit expensive just because Minecraft commands are poorly optimized. On top of this, there are a few other nuances, but the point is that the stack is not as efficient as it is in a normal system, and thus a stack is only used in datapack making when absolutely necessary.

However, we are able to leverage an advantage that Minecraft has over normal systems: the ability to create an arbitrary number of scoreboards at compile time.

If we assume that functions cannot call themselves recursively (even through other functions, i.e. if we treat all the functions as nodes, and draw an edge to other functions that the function calls, we cannot have a cycle), then we can create a fixed number of scoreboards for each function, since the amount of stack memory needed is known at compile time. Essentially we are moving as much of the stack memory to global memory.

I'm sure this seems like an awful idea, but efficiency-wise it is best.

This may seem wasteful, but it should not be too bad. There are some optimizations to reduce the number of scoreboards:

1. If two functions cannot be on the call stack at the same time, they can share the same scoreboards
2. A function only needs to store the scoreboards of the variables that are used after calling another function. If a function does not use any variable after calling another function, or does not call another function, then it can use only shared, temporary registers.

### Recursion

Unfortunately, recursive functions are useful, so we need to find a way to support them.

The solution is to use a stack, but force the programmer to explicitly tell the compiler that they want to use a stack, so they are aware of the performance implications.

By using the `rec` keyword in front of a function, the programmer can tell the compiler that they want to use a stack.

Note that the `rec` keyword is only needed for functions that contain a cycle, and not necessarily by every function that a `rec` function calls.

Then, every time a `rec` function is called, additional memory will be allocated on the stack for the function to use. Note that using macros, we can allow the function to use the stack values directly, unlike in a normal system where the function would need to copy the stack values to registers.