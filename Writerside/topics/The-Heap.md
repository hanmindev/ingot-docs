# The Heap

The stack is useful, but it has a few limitations. The main limitation is that the stack needs to have a fixed size at compile time, and anything on the stack will be removed when the function returns. This means that if you want to store a variable that has a dynamic size that you want to keep around longer, you will need to use the heap.

The heap is a region of memory that is not directly accessible by the CPU, and is used for storing variables with dynamic size and longer lifetime.

Internal fragmentation can be an issue in the heap because we want to keep data contiguously. However, in Minecraft scoreboards, there is no real concept of contiguous data, and scoreboards are index by strings anyway, so what we can do is when we allocate a set amount of data on the heap, we return the main address, suppose it is `12345`, then each block of data can be assigned to fake player `12345-0`, `12345-1`, etc. This way, we can just keep track of which main address is being used, and we can just use the fake players to index the data.

We could use a bitmap to keep track of which addresses are being used, but Minecraft does not have bitwise operations so this will be incredibly slow (we would have to use division and modulo which would be extremely expensive). Instead, we will use a simple algorithm:

Keep track of two variables:

1. Let `freed` be a stack of integers, where the top of the stack contains the most recently freed addresses.
2. Let `n` be the number of addresses that have ever been allocated.

When allocating data to the heap:

1. If `freed` is not empty, pop the top of the stack and return the popped value
2. Else, increment `n` and return the value of `n`

When freeing data from the heap:
1. Push the address to the top of `freed`

This is not incredibly fast, nor is it incredibly memory efficient, but it is simple and it works. I am open to suggestions for better algorithms.

I presume that the heap will not be used too much, so this should be fine.

## Allocating Data

To actually access the heap, use the `malloc` function, then free with the `free` function. This is just like in C.

_src/main.ing_
```C
struct Team {
    int points;
    string name;
}

fn main() {
    int *team = malloc(Struct Team);  
    
    free(team);
}
```

## Performance

Note that while the heap is incredibly useful in normal systems, it should be used cautiously in Minecraft. This is because of the poor speed of the heap implementation, even outside of heap allocation. Whilst in normal systems, heap allocation is slightly slower, but heap access is just as fast, in Minecraft, the dynamic nature of the heap requires dynamic access, which is very slow.

Frequent heap allocations and deallocations should be avoided, and variables that will be accessed frequently should be stored elsewhere such as in global memory instead, even if it is bad practice. Refer to [Global Memory](Global-Memory.md) and [Entities as Objects](Data-in-Entities.md).