# Dynamic Access

This section only exists due to the pitfalls of the mcfunction language.

Unlike other languages, dynamically choosing a part of memory to access is prohibitively expensive in mcfunction.

This is because of the limitations of mcfunction and how data storage can be accessed. For example, given a list in data storage, unlike most other languages where you can access `lst[i]` where `i` is a variable, in mcfunction you can only access `lst[0]`, `lst[1]`, `lst[2]`, etc.

So in Ingot, while you can index an index with a constant like so:

_src/main.ing_
```C
fn main() {
    int[] arr = [1, 2, 3, 4, 5];
    
    const int c = 4; // note that this is a constant, and known at compile time
    
    println!(arr[0]);
    println!(arr[c]);
```

**Sample Output**
```
1
5
```

You cannot do this:

_src/main.ing_
```C
fn main() {
    int[] arr = [1, 2, 3, 4, 5];
    
    int i = 4; // note that this is a variable, and not known at compile time
    
    println!(arr[i]);
```


With [Minecraft Snapshot 23w31a](https://www.minecraft.net/ja-jp/article/minecraft-snapshot-23w31a) and the new macro system, it is possible to access an index based on a variable, but empirical testing from various members of the community have found this to be much more expensive.

So the options are:

1. Use a chain of if statements to check if the index is 0, 1, 2, etc. and then access the corresponding variable. This is a linear search, and is O(n), only recommended for small arrays.
2. Perform a k-ary search by having the main function check the value of the index, and recurse into the corresponding function. This finds the correct index in logarithmic time, but requires a lot of functions to be generated, which kills memory usage, although it is very fast.
3. Use a Minecraft function macro to access the index. This is relatively fast and does not require a lot of functions to be generated, but is still slower than a normal function call. This is the recommended method for large arrays.

However, note that dynamic access is not only for list access. It can be used for dictionary access, rotating / translating an entity by a variable amount, moving the execution context to a variable location, etc. There are quite literally an infinite number of use cases for dynamic access because Minecraft just does not have a good way to do it. In different use cases, different methods may be more appropriate.

Thus, the programmer must choose the best method for their use case.

While the compiler could just use Minecraft function macros for all dynamic access and hide the fact that Minecraft doesn't like dynamic access from the user, we wanted the programmer to have to deliberately tell the compiler that they want to use dynamic access, so that they are aware of the performance implications.

### Linear Search of an Array
_src/main.ing_
```C
fn main() {
    int[] arr = [8, 3, 2, 9];
    for (int i = 0; i < 4; i++) {
        f_lin!(i, 0, 4, {
            println!(arr[i]);
        });
    }
}
```

**Sample Output**
```
8
3
2
9
```

Something like this is not present is any programming language (because why would you want to do this), but unfortunately this is the only way to do it in Minecraft. Since this may be confusing, let's break it down:

1. `f_lin` is a special macro which represents a linear search. We need to explicitly tell the compiler to generate extra code for the linear search.
2. The first argument is the dynamic value we want to use. In this case, it is the index of the array we want to access.
3. The second and third arguments are the start and end of the range to search (inclusive, exclusive). These must be constants.
4. The fourth argument is the code to execute when the index is found.

This is equivalent to rewriting the code like this:
_src/main.ing_
```C
fn main() {
    int[] arr = [8, 3, 2, 9];
    for (int i = 0; i < 4; i++) {
        if (i == 0) {
            println!(arr[0]);
        } else if (i == 1) {
            println!(arr[1]);
        } else if (i == 2) {
            println!(arr[2]);
        } else if (i == 3) {
            println!(arr[3]);
        }
    }
}
```


### k-ary Search of an Array
_src/main.ing_
```C
fn main() {
    int[] arr = [8, 3, 2, 9];
    for (int i = 0; i < 4; i++) {
        f_tree!(i, 0, 4, 2, {
            println!(arr[i]);
        });
    }
}
```

**Sample Output**
```
8
3
2
9
```

This is similar to the linear search, but instead of checking each index one by one, it performs a binary search.

1. `f_tree` is a special macro which represents a k-ary search. We need to explicitly tell the compiler to generate extra code for the k-ary.
2. The first argument is the dynamic value we want to use. In this case, it is the index of the array we want to access.
3. The second and third arguments are the start and end of the range to search (inclusive, exclusive). These must be constants.
4. The fourth argument is the k value. This must be a constant. This is the number of branches to split the search into. For example, if k = 2, then the search will be split into two branches, and the middle index will be checked. If k = 3, then the search will be split into three branches, and the first third, second third, and last third will be checked. The most optimal value is 3 (as opposed to 2 because... Minecraft!) but larger values can be used if memory usage is a concern (larger values will generate a smaller tree).
5. The fifth argument is the code to execute when the index is found.

This is equivalent to rewriting the code like this:
_src/main.ing_
```C
fn main() {
    int[] arr = [8, 3, 2, 9];
    for (int i = 0; i < 4; i++) {
        if (i < 2) {
            if (i <= 0) {
                println!(arr[0]);
            } else {
                println!(arr[1]);
            }
        } else {
            if (i == 2) {
                println!(arr[2]);
            } else {
                println!(arr[3]);
            }
        }
    }
}
```

This looks more complicated, but it is actually faster than the linear search. For a small array like this, the difference is negligible, but for a large array, the difference is significant; we're talking about a difference of 20 comparisons vs 1 000 000.

### Minecraft Function Macro Search of an Array

_src/main.ing_
```C
fn main() {
    int[] arr = [8, 3, 2, 9];
    for (int i = 0; i < 4; i++) {
        d_mcr!(i, {
            println!(arr[i]);
        });
    }
}
```

**Sample Output**
```
8
3
2
9
```

Unlike the linear and k-ary search, this macro leverages the new Minecraft function macro system to access the index.

1. `d_mcr` is a special macro which represents using the Minecraft function macro system.
2. The first argument is the dynamic value we want to use. In this case, it is the index of the array we want to access. In fact, we can actually add multiple arguments here, and they will be passed to the function macro. Unlike the other two macros, this is not limited to just one argument, and does not need a range to search.
3. The last argument is the code to execute when the index is found.

This doesn't really have equivalent code because the benefit comes from the Minecraft function macro system, rather than the code generated by the compiler.

Note: `d_mcr` is the only dynamic access macros that can accept non-integer arguments. This is because all the other dynamic access macros require an integer range. This means that `d_mcr` could be used to access a dictionary, or to rotate an entity by a floating point variable amount.

### Pitfalls of the Minecraft Function Macro System

It seems as though the Minecraft function macro system is the best option unless the search range is small. However, there are some pitfalls to using the Minecraft function macro system.

The main thing is that the Minecraft function macro system will literally replace every instance of the variable in the code with the value every time the function is called. While it is not an issue for this context, there are a few places where this is not desirable.

When we generate the code for the linear or k-ary search, the compiler actually goes and writes the code for each branch. This means that if there are any expressions that can be evaluated at compile time, the compiler will evaluate it at compile time, and then write the code for each branch. This means that the expression will only be evaluated once, and not every time the function is called. This increases performance, and in some cases allows the code to do things that would not be possible with the Minecraft function macro system.

However, the macro system just replaces the values at runtime, meaning that we cannot leverage the compiler to evaluate expressions at compile time. Thus, this brings us to the final option:

### Minecraft Function Ranged Macro Search of an Array

_src/main.ing_
```C
fn main() {
    int[] arr = [8, 3, 2, 9];
    for (int i = 0; i < 4; i++) {
        d_mcrr!(i, 0, 4, {
            println!(arr[i]);
        });
    }
}
```

**Sample Output**
```
8
3
2
9
```

This is similar to the Minecraft function macro search, but instead of replacing the value of the variable everywhere, it actually generates the code for each branch.

If you wanted, you could add more arguments to the macro like so:

_src/main.ing_
```C
fn main() {
    int[] arr = [8, 3, 2, 9];
    for (int i = 0; i < 4; i++) {
        for (int j = 3; j < 4; i++) {
            d_mcrr!(i, 0, 4, j, 3, 4, {
                println!(arr[i]);
                println!(arr[j]);
            });
        }
    }
}
```

**Sample Output**
```
8
2
8
9
3
2
3
9
2
2
2
9
9
2
9
9
```