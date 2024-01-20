# Modules

In this section we will learn about modules, which are a way to organize code into separate files.

## Creating a Module

Every file is a module, but we need to tell the compiler that we want to export some functions from the module.

We can do this using the `pub` keyword.

_`src/foo_file.ing`_
```C
pub fn foo() {
    println!("Hello World!");
}
```

This will export the function `foo` from the module `foo_file`.

## Importing a Module

We can import a module in the current directory using the `mod` keyword, and access the specific items using the `use` keyword.

_`src/main.ing`_
```C
use root::foo_file::foo; 

mod foo_file;

fn main() {
    foo();
}
```

This will import the function `foo` from the module `foo_file`, and allow you to use it in the current module.

If the module is in a different folder, you there needs to be a chain of imports.

_`src/foo_folder/foo_file.ing`_
```C
pub fn foo() {
    println!("Hello World!");
}
```

_`src/foo_folder.ing`_
```C
mod foo_file
```

_`src/main.ing`_
```C
use root::foo_folder::foo_file::foo; 

mod foo_folder;

fn main() {
    foo();
}
```

This will import the function `foo` from the module `foo_file` in the folder `foo_folder`.

Notice that a file with the same name as a folder can access all modules inside the folder. This is why we have a `foo_folder` file and a folder.

This is the way 

## Importing Multiple Items from a Module

You can also import multiple items from a module using braces.

_`src/main.ing`_
```C
use root::foo_file::{foo, bar}; 

mod foo_file;

fn main() {
    foo();
}
```

This will import `foo` and `bar` from the module `foo_file`.

## Importing a Module with a Different Name

You can also import a module with a different name using the `as` keyword.

_`src/main.ing`_
```C
use root::foo_file::{foo, bar as baz}; 

mod foo_file;

fn main() {
    baz();
}
```

This will import the function `foo` and `bar` from the module `foo_file`, but rename `bar` as `baz`. This is useful if there are name collisions.