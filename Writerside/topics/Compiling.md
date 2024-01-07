# Compiling

## What are we compiling to?

For most languages that compile down to a binary, the compiler will take the source code, in addition to any libraries, and compile it down to a single binary file which can be run. If the executable requires user interaction, it will typically have a loop where it will wait for user input, and then process the input and output the result.

For Minecraft however, we have decided not to use an input loop and instead let external sources call the functions directly, making the compiled output of an Ingot project more like a library than an executable.

This means that you, the datapack maker, will have to add some external logic to run the main loop and call functions based on user input. This can be easy as putting the loop function in a repeating command block or tick.json, and then calling functions based on command blocks, advancement triggers, etc.

## BlastFurnace

BlastFurnace is the name of the Ingot compiler. It is written in Rust.