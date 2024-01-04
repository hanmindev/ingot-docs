# Execution Context

This is a feature that is not present in any other programming language, but is necessary for Minecraft.

WARNING: This is a work in progress. The syntax is not finalized yet.

Execution context is the context that the currently running mcfunction function is in. It contains information such as the current position of the execution context, the current rotation of the execution context, the current entity that the execution context is in, etc.

Note that there can only be one execution context per scope, although child scopes can have a scope which appends to the parent scope. You can create scopes by adding curly braces `{}`.

_src/main.ing_
```C
fn main() {
    mcf!("say hi");
    
    ctx!("as @e[name=foo, limit=1]", {
        mcf!("say hello");
    });
}
```

**Sample Output**
```
[@] hi
[foo] hello
```

The above will run assuming that the main function was called by a command block, and that there is a targetable entity named `foo` in the world.

Note: it is possible that the entire inner scope is not run at all if the entity `foo` does not exist. Additionally, it is possible that if `limit=1` were removed, the inner scope would run multiple times if there are multiple entities named `foo`. Thus, depending on the execution context, the inner scope could be thought of as being inside a loop.

## Stacking contexts

In a loop, it might be useful to save execution contexts. For example, if you want to place several blocks in a line, you might want to change the position of the execution context by 1 block to the right every loop iteration. Thankfully, the `fctx` macro works naturally with loops.

_src/main.ing_
```C
fn main() {    
    for (int i = 0; i < 10; i++) fctx!("positioned ~ ~ ~1", {
        mcf!("setblock ~ ~ ~ stone");
    });
}
```

If we just used a `ctx` macro, it would work but the context would be applied before we placed a single block, which could be undesirable. The `fctx` macro will apply the context after the loop iteration. The `fctx` macro is short for "future context". It does not make sense to use the `fctx` macro outside a loop.

This would place 10 blocks in a line, with the first block being at the position of the execution context, and the rest being 1 block to the right of each the previous block.

Of course, if you don't want the contexts to stack, you can just use the `ctx` macro outside the loop.

## Saving contexts

Temporarily saving and restoring contexts can be useful. Positional and rotational contexts can be saved in entities and restored later using the `ctx_save` and `ctx_retrieve` macros.

TODO: add more information about this

Plans are not finalized yet.

_src/main.ing_
```C
fn main() {
    ctx_retrieve!("@e[type=marker, limit=1, tag=save_a]", "r", {
        for (int i = 0; i < 10; i++) fctx!("positioned ^ ^ ^1", {
            ctx_retrieve!("@e[type=marker, limit=1, tag=save_b]", "r", {
                for (int j = 0; j < 10; j++) fctx!("positioned ^1 ^ ^", {
                    mcf!("setblock ~ ~ ~ stone");
                });
            });
        });
    });
}
```