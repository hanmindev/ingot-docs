# Variables

Like most other languages, Ingot has variables. Variables are used to store data, which can then be used later in the program.

## Data types:

bool: boolean (stored as a 32-bit signed integer)

int: 32-bit signed integer

float: 32-bit floating point number

double: 64-bit floating point number

string: string (note there are no characters, only strings)

compound: An ordered list of attribute-value pairs. The attribute is a string, and the value can be any data type.

Data types can also be put in an array. For example, `int[]` is an array of integers. Arrays have a fixed size at compile time.

_src/main.ing_

```C
fn main() {
    int a = 1;
    float b = 2.0;
    double c = 3.0;
    string d = "Hello World!";
    compound e = { "a": 1, "b": 2.0, "c": 3.0, "d": "Hello World!" };
    int[] f = [1, 2, 3, 4, 5];
}
```

Note: variable declaration can have the `const` keyword in front of it to make it a constant. Unlike some other languages, constants can only be set to a constant expression. i.e. the compiler must be able to evaluate the value of the constant at compile time.