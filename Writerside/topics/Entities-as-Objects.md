# Entities as Objects

Treating entities as a way to store data through scoreboards is a very powerful concept in datapack creation.

Naturally, Ingot has a way to do this in a very efficient way.

## Execution Context

Ingot has a concept of an execution context. Take an execute command in Minecraft:

```
execute as Steve at @s positioned ^ ^ ^1 run setblock ~ ~ ~ stone
```

More information can be found on the [Minecraft Wiki](https://minecraft.wiki/w/Commands/execute), but essentially:

* `as Steve` sets the execution context to be the player Steve
* `at @s` sets the execution context to be at the position of the entity in the current execution context, which was just set to Steve
* `positioned ^ ^ ^1` sets the execution context to be 1 block in front of the current execution context, which was just set to Steve at the position of the entity in the current execution context, which was just set to Steve

So basically this sets a stone block 1 block in front of player Steve.

More information about the execution context can be found in the [Execution Context](Execution-Context.md) topic, but for now, all that is important is that we can use the `as <selector>` to make the function run off an entity or entities.

So we can do something like this:

```C
fn main() {
    ctx!("as @e[name=foo, limit=1]", {
        mcf!("say hello");
    });
}
```

Again, more details will be in the [Execution Context](Execution-Context.md) topic, but essentially the code inside the `ctx` macro will run as if it were run by the entity `foo`.

## Storing Data in Entities

You may want to store data inside entities. We can do this easily using the `entity` keyword.

WARNING: Similar to how declaring a global variable makes the variable accessible anywhere, using the entity keyword makes the entity variable `val` accessible anywhere. Consider adding the `static` keyword to the variable declaration to make it only accessible in the current file.

```C
fn main() {
    ctx!("as @e[name=foo]", {
        static entity int val = 3;
    });
}
```

This will store the value 3 in the entity `foo`, which can be accessed later.

In the game, a scoreboard objective with name `namespace.file_name.val` will be created.

## Accessing Data in Entities

We can access the data in entities using the `entity` keyword.

```C
fn main() {
    ctx!("as @e[name=foo]", {
        static entity int val;
        println!(val);
    });
}
```

This will print the value of the variable `val` in the entity `foo`. If there is nothing stored in the entity, it will print 0.