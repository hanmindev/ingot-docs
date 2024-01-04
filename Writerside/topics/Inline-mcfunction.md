# Inline mcfunction

While the Ingot programming language provides a lot of programming features like other languages, it actually does not do much in terms of modifying the game itself.

This is because mcfunction is already a programming language, and it is much more efficient to just use mcfunction to modify the game.

Just like how one can write inline assembly in C or Rust, one can write inline mcfunction in Ingot.

```C
fn main() {
    int a = 0;
    while (a < 10) {
        mcf!(
            "summon minecraft:armor_stand",
            "summon minecraft:pig",
        );
        a = a + 1;
    }
}
```

The above code will summon 10 armor stands and 10 pigs.

The `mcf!` macro takes a list of strings, and each string is a mcfunction command. The commands are executed in order.

### Dynamic Values

The `mcf!` macro can also take variables, and it will insert the value of the variable into the command.

```C
fn main() {
    int a = 0;
    while (a < 10) {
        d_mcr!(a, {
            mcf!(
                "summon minecraft:armor_stand ~ ~ ~ {Tags: [\"{a}\"]}",
                "summon minecraft:pig ~ ~ ~ {Tags: [\"{a}\"]}",
            );
        });
        a = a + 1;
    }
}
```

The above code will summon 10 armor stands and 10 pigs, and each armor stand and pig will have a tag from 0 to 9.

Recall from the [Dynamic Access](Dynamic-Access.md) topic that the `d_mcr` macro will allow you to use dynamic values where you normally cannot, like inside a string.

### Copying Scoreboard Values

Sometimes, it may be useful to copy the value of a variable to a scoreboard value, and vise versa. This can be done with the `mcf_input` and `mcf_output` macro.

```C
fn main() {
    int a = 10;
    mcf_input!("Steve kills", a);
    int b;
    mcf_output!(b, "Steve health");
}
```

The above code will set `Steve`'s `kills` scoreboard value to 10, and then set the variable `b` to the value of `Steve`'s `health` scoreboard value. Of course this could be done using the `d_mcr` and `mcf` macros, but this is much faster. 