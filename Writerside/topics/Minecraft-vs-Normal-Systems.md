# Minecraft vs. Normal Systems

Note this is a simplification of the actual system, but it is good enough for our purposes.


At its core, a modern computer will have:


1. Main memory (RAM), where bulk of the program data is stored.

2. Registers, which are small memory locations that are directly accessible by the CPU.


The CPU can only directly access the registers, so if it wants to say, add two numbers, it needs to load the numbers from main memory into registers, add the numbers, and then store the result back into main memory.


However, the number of registers is limited, so the CPU will need to load and store data from main memory frequently.


For example, when a function is called, all the local variables are stored in the stack, which is a region of main memory. When the function returns, the stack is popped, and the local variables are removed from memory.


Similarly, Minecraft has:

[//]: # (1. Data Storage, where a lot of data can be stored)
1. Dynamically indexed scoreboards, where an arbitrary amount of indexable data can be stored.

2. Fixed Scoreboards, where a limited amount of data can be directly accessed.