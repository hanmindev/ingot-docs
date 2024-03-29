# Global Memory

There also exists global memory, which is a region of memory that is shared between all functions, where we can store global variables.

Normally, using global variables is bad practice and should be avoided. However, in Minecraft, they allow us to bind the global variables to known scoreboard and storage locations, meaning that they can be accessed very quickly without needing to search for them.

Thus, Ingot recommends the usage of global variables as opposed to variables in the heap whenever possible. This does mean that you need to be careful about using these variables, but it is a small price to pay for the performance benefits.

Global variables are declared outside of functions. These can be used anywhere in the program, even by other libraries.

However, by default global variables are not accessible by other files. This is to prevent name collisions. 

This will not work and will result in a compile error.

_src/global.ing_
```C
let x: int = 5;
```

_src/main.ing_
```C
fn main() {
    println!(x);
}
```

If you want to make a global variable accessible by other files, you can use the `pub` keyword.

_src/global.ing_
```C
pub let x: int = 5;
```

_src/main.ing_
```C
use root::global::x

mod global

fn main() {
    println!(x);
}
```

**Sample Output**
```
5
```

Learn more about the `use`, `mod` and `pub` keywords in [Modules](Modules.md).