# unshift command

## Synopsis

```text
/unshift variable-name text
```

## Description

`unshift` prepends the supplied text to the named variable, adding one separating space before its previous value when that value is nonempty. If the variable is empty or undefined, the text becomes its value without an added trailing separator. Supply the variable's name, not a leading `$` or its expanded contents: use `/unshift items first`, rather than `/unshift $items first`.

The name is parsed as an assignment name, not a quoted word. Names are case-insensitive and support normal bracketed names. Lookup and writeback follow ordinary variable rules: an existing visible local is updated; otherwise writeback is global. `:name` forces local writeback and `::name` forces global writeback. See [`assign`](assign.md) and [`local`](local.md). Built-in and environment values can participate in lookup, but writeback changes a script assignment, not a setting or environment variable.

Everything after the name's first whitespace separator is the payload, including additional leading or trailing spaces. It is not limited to one word. Ordinary command expansion may occur before the handler receives this text. With `xdebug dword` off, the payload is prepended literally, retaining spaces and quotation marks.

With `xdebug dword` on, the entire payload is treated as one prepended item. If it contains a literal space, EPIC surrounds it with double quotes and escapes embedded double quotes. It does not separately parse each word or remove enclosing quotes supplied by the caller. Existing variable text is appended unchanged after that item. Automatic quoting tests literal spaces rather than every whitespace character.

With no arguments or no payload, the command makes no change. An invalid character directly following the parsed name causes silent rejection; other invalid assignment names can produce the normal assignment diagnostic. There are no options. The command wraps the [`unshift` function](../functions/unshift.md) and discards its returned updated value, so it has no normal output or function result. It is available in every build and requires no IRC connection.

## Examples

```text
assign items two three
unshift items one
echo $items
```

This displays `one two three`.

With double-quoted word mode enabled:

```text
xdebug dword
assign items three
unshift items one two
echo $items
```

This displays `"one two" three`. Word mode is a client-wide debug setting and also affects other word operations.

## Related commands

[`shift`](shift.md) removes the first word and discards it; its function form returns the removed word. Under matching word-mode settings, it can remove a grouped item prepended by `unshift`. Removing the only word deletes a global assigned value, while an emptied local remains defined. [`push`](push.md) appends text and [`pop`](pop.md) removes the last word. These command forms wrap their corresponding functions, but discard their results.

## Source

[source/commands.c](../../source/commands.c) registers `unshift` and implements `unshift_cmd()`. [source/functions.c](../../source/functions.c) implements `function_unshift()`. [source/alias.c](../../source/alias.c) handles variable lookup and writeback; [source/ircaux.c](../../source/ircaux.c) implements name parsing and quoting/concatenation helpers. [include/words.h](../../include/words.h) defines word modes.
