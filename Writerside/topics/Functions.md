# Functions

Functions are declared with the `fn` keyword. Functions can have parameters and return values. Unlike many other languages where the output needs to be wrapped in a struct or a class, Ingot allows functions to return multiple values.

_src/main.ing_
```C
fn main() {
    let (a, b) = sum_prod(1, 2);
    println!(a);
    println!(b);
}

fn sum_prod(a: int, b: int) -> (int, int) {
    return (a + b, a * b);
}
```

**Sample Output**
```
3
2
```

In function signatures, each parameter type must be specified.

The return type can be specified after the `->` arrow. If the function returns multiple values, the return type must be wrapped in parentheses. If the function does not return any values, the return type can be omitted. There are plans to allow the return type to be implied from the return statement, but for now everything must be explicitly typed.

