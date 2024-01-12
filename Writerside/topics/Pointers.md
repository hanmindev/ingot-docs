# Pointers

I'm assuming you know what a pointer is. If you don't, there are many resources on C-style pointers.

In Ingot, pointers are 32-bit signed integers, and are used to access memory. Recall the [Memory Model](The-Memory-Model.md).

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
1-543
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

## Optimizations

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

## Additional Considerations

If we pass a pointer to an integer into a function, in the compiled code, the pointer value will be added to a storage value, so Minecraft, using runtime macro evaluation, can insert the address of the integer into the function.

For example, for the following function:

_src/main.ing_
```C
fn multiply(int *a) {
    d_mcr!(a, {
        (*a) *= 2;
    });
}

fn main() {
    int a = 5;
    multiply(a);
}
```

The compiled code may look something like this barring additional compiler optimizations:

```
/multiply
$scoreboard players operation $(main_addr_0)-$(sub_addr_0) ingt_mem *= 2 ingt_const


/main
scoreboard players set 1-214312 ingt_mem 5
function multiply {main_addr_0: 1, sub_addr_0: 214312}
```

Let's break this down.

1. The `multiply` function is compiled into a function that takes in a main address and a sub address, and multiplies the value at that address by 2. Note that it uses a macro, since it uses non-constant pointers. You don't have to worry about the `d_mcr` macro for now, this is only needed because dereferencing a pointer is dynamic. Learn more about it in [Dynamic Access](Dynamic-Access.md).
2. The `main` function sets the value of `a` to 5, then calls the `multiply` function with the address of `a`, which happens to be `1-214312`. Recall that non `rec` functions store local variables in global memory, which is stored at main address `1`. The sub address could be anything, but for this example, we will just use `214312`.
3. Note that the address of `a` is known at compile time, so the compiler can just insert the address into the function as a macro parameter.

## Issues with Structs

This is all good, but there are a few issues concerning structs. Note that if a struct has one or no fields, then it is essentially the same as a primitive, so we don't need to worry about it.

### Issue 1: Passing struct pointers efficiently

What if we want to pass a struct pointer into a function? We can't just pass the address of the struct into the function, we would have to pass the address of each individual element of the struct into the function!

For example, consider the following function:

_src/main.ing_
```C
struct Stuff {
    int a;
    int b;
    int c;
}

fn dostuff(Stuff *s) {
    d_mcr!(s, {
        s.a = s.b + s.c;
    });
}

fn main() {
    Stuff s = {
        a: 1,
        b: 2,
        c: 3
    };
    dostuff(&s);
}
```

If we do it the naiive way, we might get something like this (barring additional compiler optimizations):
```
/dostuff
$scoreboard players operation $(main_addr_0)-$(sub_addr_0) ingt_mem = $(main_addr)-$(sub_addr_1) ingt_mem
$scoreboard players operation $(main_addr_0)-$(sub_addr_0) ingt_mem += $(main_addr)-$(sub_addr_2) ingt_mem


/main
scoreboard players set 1-214312 ingt_mem 1
scoreboard players set 1-214313 ingt_mem 2
scoreboard players set 1-214314 ingt_mem 3
function multiply {main_addr_0: 1, sub_addr_0: 214312, sub_addr_1: 214313, sub_addr_2: 214314}
```

Which is fine, because we happen to know the address of the struct at compile time, but what if we don't?


_src/main.ing_
```C
struct Stuff {
    int a;
    int b;
    int c;
}

fn dostuff(Stuff *s) {
    d_mcr!(s, {
        s.a = s.b + s.c;
    });
}

fn main() {
    Stuff *s = malloc(Stuff);
    
    dostuff(s);
}
```

Now we don't know the address of the struct at compile time, so we might get something like this (again, barring additional compiler optimizations):

```
/dostuff
$scoreboard players operation $(main_addr_0)-$(sub_addr_0) ingt_mem = $(main_addr_1)-$(sub_addr_1) ingt_mem
$scoreboard players operation $(main_addr_0)-$(sub_addr_0) ingt_mem += $(main_addr_2)-$(sub_addr_2) ingt_mem


/main
scoreboard players set 1-214312 ingt_mem 2
scoreboard players set 1-214313 ingt_mem 0
scoreboard players set 1-214314 ingt_mem 2
scoreboard players set 1-214315 ingt_mem 1
scoreboard players set 1-214316 ingt_mem 2
scoreboard players set 1-214317 ingt_mem 2 # assume these are the addresses of the struct fields returned from malloc

execute store result storage ingt:fn_mcr params.main_addr_0 int 1 run scoreboard players get 1-214312 ingt_mem
execute store result storage ingt:fn_mcr params.sub_addr_0 int 1 run scoreboard players get 1-214313 ingt_mem
execute store result storage ingt:fn_mcr params.main_addr_1 int 1 run scoreboard players get 1-214314 ingt_mem
execute store result storage ingt:fn_mcr params.sub_addr_1 int 1 run scoreboard players get 1-214315 ingt_mem
execute store result storage ingt:fn_mcr params.main_addr_2 int 1 run scoreboard players get 1-214316 ingt_mem
execute store result storage ingt:fn_mcr params.sub_addr_2 int 1 run scoreboard players get 1-214317 ingt_mem

function multiply with ingt:fn_mcr params 
```

Wow, that's long! Let's break it down.

1. The `dostuff` function is still identical as before.
2. We didn't add the code for what the actual malloc function might look like. instead, we replaced it with six scoreboard values that save the address of the struct fields. Since the data is in the heap, the main address starts with `2`, and the sub address starts at `5324234` (some random address returned by malloc that isn't already used), and increments by 1 for each field.
3. Then, we copy over the values of the scoreboard values into the storage values, so we can use them in the macro.
4. Finally, we call the `dostuff` function with the storage values.

Notice that we need six storage calls to store the addresses of the struct fields, and we need six execute calls to copy the values from the scoreboards to the storage values. Our struct only has three fields, but it's already this long. Imagine if we had a struct with ten, or a hundred fields!

Of course, we can still optimize this by taking into account that all the data is from the heap, and thus they all have the same main address, but we still need to copy over the sub addresses.

We are in luck. There is a clever trick we can use to reduce the number of storage and execute calls.

#### The solution

The solution is to leverage the fact that function macros are able to concatenate strings. If we let the sub address of the struct start at a multiple of 10, then we can hardcode the last digit of the sub address into the function, and just pass the all the digits except the last one into the function. This also means that our malloc call only needs to return one main-sub address pair.


```
/dostuff
$scoreboard players operation $(main_addr_0)-$(sub_addr_0)0 ingt_mem = $(main_addr_0)-$(sub_addr_0)1 ingt_mem
$scoreboard players operation $(main_addr_0)-$(sub_addr_0)0 ingt_mem += $(main_addr_0)-$(sub_addr_0)2 ingt_mem


/main
scoreboard players set 1-214312 ingt_mem 2
scoreboard players set 1-214313 ingt_mem 0 # start of the struct address

execute store result storage ingt:fn_mcr params.main_addr_0 int 1 run scoreboard players get 1-214312 ingt_mem
execute store result storage ingt:fn_mcr params.sub_addr_0 int 0.1 run scoreboard players get 1-214313 ingt_mem

function multiply with ingt:fn_mcr params 
```

Let's break it down.

1. The `dostuff` function has slightly changed. Note that the macros only have `main_addr_0` and `sub_addr_0`, as opposed to having `main_addr_1`, `main_addr_2`, `sub_addr_1`, and `sub_addr_2` as before. This is because we are able to hardcode the last digit of the sub address into the function.
2. The `main` function is also slightly changed. Note that the sub address of the struct starts at `5324230`, which is a multiple of 10. Then, notice that we divide the sub address by 10 when copying it over to the storage value. This is because we want to remove the last digit of the sub address, since we are hardcoding it into the function.

This is much better! We only need two storage calls and two execute calls, as opposed to six storage calls and six execute calls.

Of course, this only works for structs with up to 10 fields since we are hardcoding the last digit of the sub address. If we have a struct with more fields, instead of hardcoding the last digit, we can hardcode the last two digits, or the last three digits, etc. 

However, for practical reasons, we will only support structs with 100 fields. If you have anything larger, you should probably rethink your design.

### Issue 2: Structs inside Structs (inside Structs?)...

What if we have a struct inside a struct? Recall that we require struct addresses to be aligned to the nearest multiple of 10 or 100. This means that if we have a struct inside a struct, we need to ensure that the inner struct will be aligned to the nearest multiple of 10 or 100. If we didn't, and wanted to use the address of the inner struct, we would not be able to.

Consider the following struct(s):

_src/main.ing_
```C
struct Inner {
    int a;
    int b;
}

struct Outer {
    int c;
    Inner d;
    Inner e;
    int f;
}
```

To fix this problem, we can store `Outer` like so:

abcf______ab

At some sub address which is a multiple of `100`. Suppose the sub address was `12300`. The underscores represent padding.

This way, the address of the first `Inner` struct inside the `Outer` struct will be aligned to 10 as needed, as it will have a sub address of 12300. The address of the second `Inner` struct inside the `Outer` struct will be shifted to the right by 10, so it will have a subaddress of 12310, which is still aligned to 10. Rest of the fields can be stored as normal at the end, or in between the structs in the padding, like shown above.

Note that there are a few caveats with this method:
1. We cannot have a struct which is three levels deep, as the outer struct would have to be aligned to 1000. This is not a limitation of the method, but rather a design decision to limit the complexity of the compiler.
2. Each inner struct takes up 10 addresses worth of space (unless positioned at the very end), even if it is smaller than 10 addresses. This is because we need to ensure that the inner struct is aligned to 10. This could potentially make certain structs take up more space than they need to, but this is a tradeoff we have to make for speed.

The wasted memory issue can be alleviated by storing other variables, if there are any, between the padding. For example, if we have an instance of the `Outer` struct on the stack, we can store some other stack variables in the padding.

The three-level-struct problem can be alleviated by storing the inner struct somewhere else and storing a pointer to it instead.