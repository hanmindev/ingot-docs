# Minecraft v.s. Normal Systems

In a simplified memory model, a normal system has three main components of memory: the stack, the heap, and the data segment (global memory). There is also other components like the code segment, but it is not relevant here.

Normal system memory can be viewed as one large array of bytes, where each byte has an address which is its index in the array.

In a normal system, the stack, heap, data segment, and other components are all stored in this array of bytes. The stack is at the top of the array and grows downwards, the heap is near the bottom of the array and grows upwards, and the data segment is below the heap. The stack and the heap share the same space, and they grow towards each other.

If there are values that are larger than one byte, they will take up multiple contiguous bytes in the array.

The memory model in Minecraft is similar, but there are a few key differences.

### Address Format

Since everything in Minecraft is virtual, we are able to split the stack, heap, and the data segment.

In Ingot, memory addressing will be done using two scoreboards, which are 32-bit signed integers. The first scoreboard will be the main address, and the second scoreboard will be the sub address.

If the main address is `0`, then the whole address will be considered `null'.

If the main address is `-1`, then the address will be considered to be in global memory at the sub address.

If the main address is less than `-1`, then the address will be considered to be on the stack.

If the main address is positive, then the address will be considered to be on the heap at the sub address.

This may seem odd, and one may question why we have decided to split up the addresses this way. This is because by having a separate main address and a sub address, for any `rec` functions, the compiler can just write one function with the sub addresses filled in, then fill in the main addresses using a macro, which only supports string concatenation and not integer addition.

For the stack, the main address can be thought of as the stack frame, and the sub address can be thought of as the offset from the stack frame.

For the heap, the main address can be thought of as the heap block, and the sub address can be thought of as the offset from the heap block.

Addresses will be written out as `main_sub`.

### Memory Locations

Minecraft has two different places it can store data: in scoreboards, but also in storage.

This means that while integers can be stored in scoreboards, other values, such as floats and strings, will need to be stored in storage.

Given an address, we will have two places to look for the data! This is not a problem however as Ingot has a strict type system, so we will know where to look for the data in compile time.