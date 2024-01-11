# Pointers

I'm assuming you know what a pointer is. If you don't, there are many resources on C-style pointers.

You can get the address of a variable using the `&` operator, and dereference a pointer using the `*` operator.

_src/main.ing_
```C
fn main() {
    int a = 5;
    
    int *p = &a;
    
    println!(p);
    
    println!(*a);
}
```

**Sample Output**
```
543
5
```

Note that the first printed value is the address to the variable `a` and may not be the same all the time. The second printed value is the value of `a`, which was set to 5.

This allows us to do something like pass a large struct into a function without having to copy every single element:

_src/main.ing_
```C
struct ManyValues {
    int a;
    int b;
    int c;
    int d;
    int e;
    int f;
    int g;
}

fn multiply(ManyValues *mp) {
    return mp.a * mp.b * mp.c * mp.d * mp.e * mp.f * mp.g;
}


fn main() {
    ManyValues mv = {
        a: 1,
        b: 2,
        c: 3,
        d: 4,
        e: 5,
        f: 6,
        g: 7
    }
    
    println!(multiply(&mv));
}
```

**Sample Output**
```
5040
```

Unfortunately, unlike normal systems where pointers are incredibly fast, in Minecraft, dynamic access is very slow because the game actually has to look for the address being used. Thus, it is recommended that pointers be used sparingly.

There are a few optimizations that Ingot offers, however. If the pointer address is known at compile time, for example because the address points to a variable in global memory, or the address points to a variable in a non `rec` function, we can have a few speedups. Recall: [The Stack](The-Stack.md)

Note in the previous example, we actually know `&mv` at compile time because it is in a non `rec` function.

Thus, we can actually tell the compiler to generate a new function specifically for this address using the `hc` macro.

_src/main.ing_
```C
struct ManyValues {
    int a;
    int b;
    int c;
    int d;
    int e;
    int f;
    int g;
}

fn multiply(ManyValues *mp) {
    return mp.a * mp.b * mp.c * mp.d * mp.e * mp.f * mp.g;
}


fn main() {
    ManyValues mv = {
        a: 1,
        b: 2,
        c: 3,
        d: 4,
        e: 5,
        f: 6,
        g: 7
    }
    
    const int *mp = &mv; // we could also just use &mv directly, but this is just to show that the address of mv is known at compile time
        
    println!(hc!(multiply(mp)));
}
```

This will generate a whole new function, but because the function is already generated at compile time, should be faster to run.