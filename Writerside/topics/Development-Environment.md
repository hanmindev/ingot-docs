# Development Environment

Let's see how a simple Ingot project is set up.

## Setting up a new project

To get up a new project, create a folder to store your project(s) in. Then, create a new project using BlastFurnace.

```
blstf new hello_world
cd hello_world
```

This will create a new project with the name `hello_world` in the current directory.

Look at the generated `blstf.toml` file.

```
[package]
name = "hello_world"
version = "0.1.0"
```

This is the project configuration file. It contains the following fields:

1. `name` is the name of the project. This is used as the namespace of the function. This should be unique as to not conflict with other projects. When the folders are generated, the namespace will be used as the folder name, with `ingt_` prepended to it.
   * The name should only have alphanumeric characters and underscores. 
2. `version` is the version of the project. For now this does nothing, although it may be used for dependencies in the future.

Look at the generated `.env` file.

```
PATH_TO_FUNCTION_OUTPUT = "%appdata %\.minecraft\saves\<name>\datapacks\<datapack>\data"
```
(mind the space between the `%appdata` and the `%`, for some reason Writerside won't compile if I don't put a space there, and escaping it doesn't work)

This is the path to the folder where the compiled functions will be placed. This is the path to the `data` folder of the datapack. You need to replace `<name>` with the name of the world, and `<datapack>` with the name of the datapack. For example, if the world is named `world` and the datapack is named `my_datapack`, then the path would be `\%appdata \%\.minecraft\saves\world\datapacks\my_datapack\data`.

Note that this means you are responsible for creating the datapack and world, which you probably should know if you are looking at this guide.

The reason that this is in a `.env` file is because this is a path that is specific to your computer, and you probably don't want to commit this to git.

Look inside the generated `src` folder.

There should be one file, `main.ing`. This is the main file of the project. Look inside it.

```C
fn main() {
    mcf!("say Hello World!");
}
```

This is the main function of the project. This will be the entry point of the project, and where you should be putting your code.

