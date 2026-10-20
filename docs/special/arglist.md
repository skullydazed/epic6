# Argument lists

Argument lists automatically move supplied arguments from `$*` into local variables. They can be used with [alias](../commands/alias.md) and [on](../commands/on.md) definitions. The `fix_arglist` function parses and prints the same descriptors.

## Behavior

Without an argument list, a handler normally reads positional arguments such as `$0`, `$1`, and `$2-` from `$*`. For example:

```text
alias oofda {
    local var1 $0;
    local var2 $1;
    local var3 $2-;
    echo $var1 and $var2 and $var3!;
};
```

An argument list performs the local assignments before the body runs:

```text
alias oofda (var1, var2, var3) {
    echo $var1 and $var2 and $var3!;
};
```

Here the first two variables consume one word each, and `var3` receives the entire remainder. `$*` is then empty. Positional arguments in the body refer to the remaining argument text, not the original call. If the original text is needed later, preserve it outside the argument-list processing or use a list such as `(...)` and parse it yourself.

Hook fields can be named in the same way:

```text
on ^join * (nick, chan, userhost, extra) {
    xecho -b $nick!$userhost has joined $chan $extra;
};
```

The argument list does not alter the event's matching pattern or supply fields absent from the event. Consult the individual hook's documented arguments.

## Syntax

```text
(<name> [<word-type> <count>] [default "<value>"], ...)
```

Separate descriptors with commas and enclose the list in parentheses in a definition. Each named descriptor creates a local variable. Its default word type is `words`, its default count is `1`, and its default fallback value is empty.

| Descriptor | Meaning |
| --- | --- |
| `name` | Consume one word, except when it is the final ordinary descriptor. |
| `name words N` | Consume `N` words with quote behavior controlled by `xdebug dword`. |
| `name uwords N` | Consume `N` unquoted words: spaces separate words even inside double quotes; quote characters remain. |
| `name qwords N` | Count double-quoted groups as words and retain their quote syntax. |
| `name dwords N` | Count double-quoted groups as words and dequote the assigned result. |
| `name default "value"` | Use the fallback when no nonempty raw value was extracted. |
| `...` | Final marker: retain the unconsumed text in `$*`. |
| `void` | Final marker: discard the unconsumed text. |

Modifiers can be combined, for example `name dwords 1 default "guest"`. Use positive integer counts and valid local variable names. `void` and `...` stop parsing the list; descriptors written after them are not processed. Use three literal periods for `...`, not the Unicode ellipsis character.

**The final named descriptor takes all remaining text unless the list ends with `void` or `...`.** Its stated word count does not limit that remainder, though its quote/dequote mode still applies. To consume exactly one quoted argument and ignore extra arguments, use `(name dwords 1, void)`, not simply `(name dwords 1)`.

## Quoting and defaults

`words` follows `xdebug dword`: with it off, quotes are ordinary characters; with it on, quoted groups count as words, and a one-word descriptor dequotes its result. A multiword `words` descriptor retains quoting. Explicit `uwords`, `qwords`, and `dwords` avoid dependence on that switch.

Defaults are stored when the definition is parsed and expanded when the handler is called, after earlier descriptors have been assigned. Expansion sees the remaining `$*`, so a fallback can refer to earlier local parameters or the remaining arguments. The default's surrounding definition-time quotes group its text; dequoting appropriate to the selected word type also happens after default expansion.

An explicitly supplied `""` is nonempty raw quote syntax before dequoting, so it can produce an empty variable without selecting the fallback. Missing arguments instead use their defaults, or empty strings when no default is specified. There is no automatic required-argument, type, or arity validation.

## Examples

```text
alias greet (who dwords 1 default "guest", ...) {
    echo Hello $who (remaining: $*);
};
greet "two words" extra text
```

`who` receives `two words`, while `$*` contains `extra text`. With no supplied argument, `who` receives `guest`.

```text
alias take_two (first uwords 1, second uwords 1, void) {
    echo First: $first, second: $second;
};
```

This consumes two space-delimited words and discards any remainder. Without `void`, the second variable would collect the remainder instead.

`$fix_arglist(first dwords 1, rest)` returns a normalized textual descriptor list; pass the contents without the outer definition parentheses. It does not call a handler, assign parameters, or validate every unsafe count or name.

## Limits

Argument parsing changes the call's argument view before execution and does not keep an original-text expando for recovery. It trims separators during word extraction and is not an exact byte-preserving record of the original call. Normal local-variable scope and script expansion rules apply.

Unknown modifiers can print a diagnostic, but malformed lists are not comprehensively rejected. The implementation stores descriptors in fixed arrays of 32 entries, including a terminating entry; keep lists to at most 31 named parameters. Do not rely on the parser to enforce that boundary safely.

This is built-in language syntax, with no external program, server request, or optional platform feature required. Arguments are strings; the list itself has no function return value beyond the local assignments and remaining `$*` it establishes.

## Source references

- [source/alias.c](../../source/alias.c): `parse_arglist`, `prepare_alias_call`, and `print_arglist` implement descriptors, defaults, terminal markers, and normalization.
- [include/alias.h](../../include/alias.h): fixed argument-list storage.
- [source/ircaux.c](../../source/ircaux.c) and [source/words.c](../../source/words.c): word extraction and dequoting.
- [source/commands.c](../../source/commands.c): handler invocation prepares arguments before executing the body.
- [source/functions.c](../../source/functions.c): `function_fix_arglist` parses and prints descriptor text.
