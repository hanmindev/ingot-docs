# Modules

In this section we will learn about modules, which are a way to organize code into separate files.

## Creating a Module

Every file is a module, but we need to tell the compiler that we want to export some functions from the module.

We can do this using the `export` keyword.

_`src/foo_file.ing`_
```C
export fn foo() {
    println!("Hello World!");
}
```

This will export the function `foo` from the module `foo_file`.

## Importing a Module

We can import a module using the `import` keyword.

_`src/main.ing`_
```C
import { foo } from "foo_file";

fn main() {
    foo();
}
```

This will import the function `foo` from the module `foo_file`, and allow you to use it in the current module.

If the module is in a different folder, you can specify the path to the module.

_`src/main.ing`_
```C
import { foo } from "foo_folder/foo_file";

fn main() {
    foo();
}
```

This will import the function `foo` from the module `foo_file` in the folder `foo_folder`.

## Importing All Functions from a Module

You can also import all functions from a module using the `*` wildcard.

_`src/main.ing`_
```C
import * from "foo_file";

fn main() {
    foo();
}
```

This will import all functions from the module `foo_file`.

## Importing a Module with a Different Name

You can also import a module with a different name using the `as` keyword.

_`src/main.ing`_
```C
import { foo as bar } from "foo_file";

fn main() {
    bar();
}
```

This will import the function `foo` from the module `foo_file` and allow you to use it as `bar`. This is useful if there are name collisions.