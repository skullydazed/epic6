# $loadinfo()

## Synopsis

`$loadinfo()`

## Technical

Returns information about the innermost active [load](../commands/load.md) context. Arguments are ignored. The returned string contains these fields, separated by literal spaces:

| Position | Field |
| --- | --- |
| `0` | Current loader line counter. |
| `1` | Filename resolved for the load. |
| `2` | Loader name: normally `std` or `pf`. |
| `3` onward | Current package text, which can be empty or contain spaces. |

Outside loading, the exact result is `-1` followed by three spaces. Its first word is therefore `-1`, but the whole result is not the literal string `-1`.

The standard loader advances its line counter as it reads the file. The preformatted loader does not update this counter, so it remains `1` during normal execution; do not treat it as a precise source location. Multiline standard-loader statements can also be associated with the reader's current line rather than the statement's first line.

Nested loads temporarily replace the reported context and restore the outer context afterward. The package comes from [package](../commands/package.md) or [subpackage](../commands/subpackage.md), and can be inherited from an enclosing load. The information describes active loading, not the file that originally defined an alias called later.

Fields are not quoted or escaped. Filenames or package names containing spaces make ordinary word-position extraction ambiguous. The internal loader name `which` is also used by [which](../commands/which.md), although that loader prints the path rather than executing the file.

## Practical

Use this to inspect the loader while a script is being loaded. A simple filename without spaces can use the following guard to request the preformatted loader:

```text
if (word(0 $loadinfo()) >= 0 && word(2 $loadinfo()) != [pf]) {
    load -pf $word(1 $loadinfo())
    return
}
```

This depends on executing inside a load context and on the filename occupying one word. The return stops the outer load after the nested load finishes.

## Returns

The current line counter, filename, loader name, and package text. There is no separate error result and the function changes no state.

## Source

Registered and implemented as `function_loadinfo` in [source/functions.c](../../source/functions.c). Load contexts, field accessors, nested-load handling, and `std`/`pf` loaders are in [source/commands.c](../../source/commands.c).
