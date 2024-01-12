# The Memory Model

In a normal system, the memory model is relatively simple. The main components of memory that the programmer needs to be aware of are the stack and the heap.

At its essence, the stack is used for local variables with fixed size and limited lifetime, and the heap is used for variables with dynamic size and longer lifetime.

However, the memory model that Ingot works with is much more complicated. To understand why, we need to compare Minecraft and a normal system.