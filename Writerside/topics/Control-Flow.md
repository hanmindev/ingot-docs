# Control Flow

Conditional statements are the heart of any programming language.

## If-else

_src/main.ing_
```C
fn main() {
    int a = 1;
    int b = 2;

    if (a > b) {
        println!(a);
    } else if (a < b) {
        println!(b);
    } else {
        println!(-1);
    }
}
```

For now the parentheses around the condition are required.

## While

_src/main.ing_
```C
fn main() {
    int a = 0;
    while (a < 10) {
        println!(a);
        a = a + 1;
    }
}
```

**Sample Output**
```
0
1
2
3
4
5
6
7
8
9
```

Similarly, the parantheses around the condition are required.

## For

_src/main.ing_
```C
fn main() {
    for (int i = 0; i < 10; i++) {
        println!(i);
    }
}
```

**Sample Output**
```
0
1
2
3
4
5
6
7
8
9
```

This is a C-style for loop. The parentheses around the condition are required.

## Break and Continue

_src/main.ing_
```C
fn main() {
    for (int i = 0; i < 10; i++) {
        if (i == 5) {
            break;
        }
        println!(i);
    }
}
```
**Sample Output**
```
0
1
2
3
4
```

_src/main.ing_
```C
fn main() {
    for (int i = 0; i < 10; i++) {
        if (i == 5) {
            continue;
        }
        println!(i);
    }
}
```
**Sample Output**
```
0
1
2
3
4
6
7
8
9
```

This is similar to many other languages.