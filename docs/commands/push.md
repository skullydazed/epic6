# push

Append text to a variable's value.

## Synopsis

```text
/push variable-name text
```

## Description

`push` appends the supplied text to the named variable, inserting one space when the existing value is nonempty. With an empty or undefined variable, the supplied text becomes its value without an added leading separator. Supply the variable name itself, not a leading `$` or its expanded contents. For example, use `/push booya four`, rather than `/push $booya four`.

The variable name is parsed as an assignment name, not a quoted word. It is case-insensitive and uses normal variable lookup and assignment rules, including bracketed names. An existing visible local is updated; otherwise writeback is global. `:name` forces local writeback and `::name` forces global writeback. See [assign](assign.md) and [local](local.md).

Everything after the name's first whitespace separator is the payload; it is not limited to one word. Only that first separator is consumed, so additional leading or trailing spaces in the received payload are preserved. Ordinary command expansion can occur before this handler receives the text. With `xdebug dword` off, the payload is appended literally, retaining embedded spaces and quotation marks.

With `xdebug dword` on, the entire payload is treated as one appended item. When it contains a literal space, EPIC surrounds it with double quotes and escapes embedded double quotes. It does not independently parse and append each supplied word or strip user-supplied enclosing quotes. This allows the [pop](pop.md) operation, under the same word mode, to remove that grouped item in one operation. Automatic quoting here tests literal spaces, rather than every whitespace character.

With no arguments or no payload, the command makes no change. An invalid character directly following the parsed variable name causes a silent rejection. There are no options. The built-in is always registered and requires no IRC connection.

## Output and examples

The command wraps the [push function](../functions/push.md) and discards its returned updated value. It has no normal output or result value.

```text
assign booya one two three
push booya four
echo $booya
```

This displays `one two three four`.

With double-quoted word mode enabled:

```text
xdebug dword
assign booya one
push booya two three
echo $booya
```

This displays `one "two three"`. The two-word payload has become one quoted item. Word mode is a client-wide debug setting; changing it affects other word operations as well.

## Related commands

`pop` removes the final word, deleting a global variable's assigned value when no words remain; an emptied local remains defined. The [unshift function](../functions/unshift.md) prepends an item. [stack](stack.md) is a separate mechanism for saving definitions and settings.

## Source

Registration and `push_cmd` are in [source/commands.c](../../source/commands.c). `function_push` is in [source/functions.c](../../source/functions.c), variable lookup/writeback in [source/alias.c](../../source/alias.c), and name parsing and append/quoting helpers in [source/ircaux.c](../../source/ircaux.c). Word-mode definitions are in [include/words.h](../../include/words.h).
