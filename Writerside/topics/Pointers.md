# Pointers

Now that we have the stack and the heap, we can finally talk about pointers.

Pointers are an incredibly fast and efficient way to do things in a normal system, but implementing them directly would have poor performance in Minecraft due to the dynamic nature of pointers. Thus, we need to be more clever.

We can abstract away the need for pointers by 

## Pointers to the Stack

### Pointers to Local Variables

Pointers to local variables are relatively simple. As long as we forbid pointer arithmetic, we can 

### Pointers to Previous Non-Rec Stack Frames

Recall that without any functions with the `rec` keyword, each function has its own "stack frame" allocated with registers. 



## Pointers to the Heap