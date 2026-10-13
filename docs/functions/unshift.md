# unshift function

## Synopsis

`$unshift(<variable name> [<data>])`

## Technical

Prepends data to the named variable and returns the new value. An empty or undefined variable receives the data without an added separator. A nonempty old value follows the new data with one separating space. Supply the variable's name, not a leading `$` or its expanded contents.

The entire data argument is one prepended item. With [`xdebug dword`](../commands/xdebug.md) enabled, data containing a literal space is surrounded by double quotes and embedded quotation marks are escaped. Data without a literal space is not automatically quoted. With that flag disabled, the data is prepended literally. This operation does not split data into words or remove user-supplied quotation marks.

The variable name is parsed as an assignment name, not a quoted word. Names are case-insensitive and support normal bracketed-name and scope rules. An existing visible local variable is updated; otherwise writeback is global. `:name` forces local writeback and `::name` forces global writeback. See [`local`](../commands/local.md) and [`assign`](../commands/assign.md).

Only the first whitespace separator after the name is consumed; additional received whitespace remains part of the data. Ordinary function-argument expansion occurs before this parsing.

## Practical

Where [`shift`](shift.md) removes the first item, `unshift` adds an item at the front. [`push`](push.md) appends at the end, and [`pop`](pop.md) removes the last item. Use consistent quoted-word settings when adding and removing grouped items.

The [`unshift` command](../commands/unshift.md) invokes the same operation and discards the return value.

## Returns and restrictions

The new variable value after prepending. With a valid name but no data, returns the current value without writeback. Missing input or an invalid character directly after the parsed name returns empty without prepending. Assignment validation can print a diagnostic; the returned string alone is not proof that writeback succeeded. There are no additional options or server requirements.

## Examples

```irc
assign -foo
$unshift(foo blah)     Assigns and returns blah.
$unshift(foo booya)    Assigns and returns booya blah.
```

After `assign foo old`, `$unshift(foo hello there)` returns `"hello there" old` with `xdebug dword` enabled, or `hello there old` with it disabled. In either case, the returned value is assigned to `foo`.

## Source

Handler: [`source/functions.c`](../../source/functions.c:3656). Assignment-name parsing and word quoting: [`source/ircaux.c`](../../source/ircaux.c). Variable lookup and writeback: [`source/alias.c`](../../source/alias.c:1059).
