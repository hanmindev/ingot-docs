# Minecraft v.s. Normal Systems

In a simplified memory model, a normal system has three main components of memory: the stack, the heap, and the data segment (global memory). There is also other components like the code segment, but it is not relevant here.

Normal system memory can be viewed as one large array of bytes, where each byte has an address which is its index in the array.

In a normal system, the stack, heap, data segment, and other components are all stored in this array of bytes. The stack is at the top of the array and grows downwards, the heap is near the bottom of the array and grows upwards, and the data segment is below the heap. The stack and the heap share the same space, and they grow towards each other.

If there are values that are larger than one byte, they will take up multiple contiguous bytes in the array.

The memory model in Minecraft is similar, but there are a few key differences.

First, since everything is virtual, we are able to split the data segment away from the stack and the heap.

Second, because scoreboards are indexed by name rather than by address, we can make two-dimensional addresses. For example, instead of storing a 4-byte variable at addresses 1000, 1001, 1002, 1003, we can store it at addresses 1000-0, 1000-1, 1000-2, 1000-3. This will help us with dynamic memory access.

We will call the first part of the address the main address, and the second part of the address the sub address, and the whole thing the address.

Note: Structs with Structs inside of them will have the inner struct(s) stored in the next address. For example:

```C
struct A {
    int a0;
    int b0;
    struct B c;
}

struct B {
    int a1;
    int b1;
}
```

If we want to store struct A in main address 1000, a0 would be stored at 1000-0, b0 would be stored at 1000-1, a1 would be stored at 1001-0, and b1 would be stored at 1001-1. 