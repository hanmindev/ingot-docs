# Variables

Like most other languages, Ingot has variables. Variables are used to store data, which can then be used later in the program.

## How Minecraft stores data

There are two main ways that mcfunction can store data:

1. In a scoreboard
2. In data storage

Unfortunately, both of these have limitations.

Scoreboards store 32-bit signed integers, so they can only store integers and booleans.

Data storage can store any data type, but a lot of the data cannot be modified in a meaningful way. For example, in order to perform integer arithmetic, integers must be copied to a scoreboard, modified, and then copied back to the data storage.

## Data types:

### Scoreboard and Data Storage Data Types

bool: boolean (stored as a 32-bit signed integer)

int: 32-bit signed integer

### Data Storage Data Types

float: 32-bit floating point number

double: 64-bit floating point number

string: string (note there are no characters, only strings)

compound: An ordered list of attribute-value pairs. The attribute is a string, and the value can be any data type. Declare using curly braces `{}`. Example: `{ "a": 1, "b": 2.0, "c": 3.0, "d": "Hello World!" }`. Read more in [Bundling Data](Bundling-Data.md).

list: An ordered list of values which have dynamic size. The values can be any one data type. Declare using `{}`. Example: `{ 1, 2, 3, 4, 5 }`

All data types can also be put in an array. For example, `int[]` is an array of integers. Arrays have a fixed size at compile time. Declare using `{}`. Example: `{1, 2, 3, 4, 5}`

_src/main.ing_

```C
struct Example_Struct {
    let a; int;
    let b; float;
    let c; double;
    let d; string;
} // see Bundling Data for more information on structs

fn main() {
    let a: bool = true;
    let b: int = 1;
    let c: float = 2.0;
    let d: double = 3.0;
    let e: string = "Hello World!";
    let f: compound<Example_Struct> = { "a": 1, "b": 2.0, "c": 3.0, "d": "Hello World!" };
    let g: int[] = {1, 2, 3, 4, 5};
    let h: list<int> = { 1, 2, 3, 4, 5 };
}
```

## Constants

Variables can be declared with the `const` keyword to make it a constant.

_src/main.ing_
```C
const a: int = 1;
```

In Ingot, constants are immutable values that are known at compile time. This means that the compiler must be able to evaluate the value of the constant at compile time.

This means that unlike some other languages where "constant" just means "immutable after initialization", Ingot constants cannot be initialized from non-constant variables, with a function call, or any other expression that cannot be evaluated at compile time.