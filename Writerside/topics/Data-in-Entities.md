# Storing Data in (Tile) Entities

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

## Storing Integer Values in Entities

You may want to store data inside entities. We can do this easily using the `entity` keyword. Unfortunately because Minecraft does not allow arbitrary data storage in entities, so only values that can be stored in a scoreboard (integers, booleans) can be stored in entities.

```C
fn main() {
    ctx!("as @e[name=foo]", {
        entity int val = 3;
    });
}
```

This will store the value 3 in the entity `foo`, which can be accessed later.

In the game, a scoreboard objective with name `namespace.file_name.val` will be created.

Note: You may want to allow other files to access the value of `val`. You can do this by exporting the value of `val`:

```C
export entity int val;

fn main() {
    ctx!("as @e[name=foo]", {
        val = 3;
    });
}
```

## Accessing Integer Values in Entities

We can access the data in entities using the `entity` keyword.

```C
fn main() {
    ctx!("as @e[name=foo]", {
        entity int val;
        println!(val);
    });
}
```

This will print the value of the variable `val` in the entity `foo`. If there is nothing stored in the entity, it will print 0.

Of course, we can modify the value of the variable `val` in the entity `foo`:

```C
fn main() {
    ctx!("as @e[name=foo]", {
        entity int val;
        val += 3;
        
        println!(val);
    });
}
```

This will add 3 to the value of the variable `val` in the entity `foo`.

## Storing Storage Values in Entities

Although we cannot store arbitrary data in entities, if the data is part of the entity's NBT, we can store it in the entity. We can use the `entity_nbt` macro to do this.

For example, if we want to modify the entity's position, we can do so like this:

```C
fn main() {
    list<double> pos = { 1.0, 2.0, 3.0 };

    ctx!("as @e[name=foo]", {
        entity_nbt!("Pos") = pos;
    });
}
```

This will set the position of the entity `foo` to be (1.0, 2.0, 3.0).

## Accessing Storage Values in Entities

We can access the data in entities using the `entity_nbt` macro.

```C
fn main() {
    ctx!("as @e[name=foo]", {
        list<double> pos = entity_nbt!("Pos");
        println!(pos);
    });
}
```

This will print the position of the entity `foo`.

Note: `pos` and `entity_nbt!("Pos")` are not the same, but rather a deep copy of the data is made. This means that modifying `pos` will not modify the entity's position.

You can either:

1. Modify the entity's position directly using `entity_nbt!("Pos")`
2. Modify `pos` and then set the entity's position using `entity_nbt!("Pos") = pos`

The first option is more efficient.

## Storing Storage Values in Tile Entities

Similar to how we can store data in entities, we can also store data in tile entities. We can use the `block_nbt` macro to do this.

```C
fn main() {
    ctx!("positioned 0 0 0", {
        block_nbt!("IsPlaying") = false;
}
```

If there is a jukebox at position (0, 0, 0), this will set the `IsPlaying` tag to false.

## Accessing Storage Values in Tile Entities

We can access the data in entities using the `block_nbt` macro.

```C
fn main() {
    ctx!("positioned 0 0 0", {
        bool playing = block_nbt!("IsPlaying");
        
        println!(playing);
    });
}
```

If there is a jukebox at position (0, 0, 0), this will print the value of the `IsPlaying` tag.

## Details of how NBT access works

When you use the `entity_nbt` or `block_nbt` macro to assign the value of a variable, 