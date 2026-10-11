# fix_arglist function

## Synopsis

`$fix_arglist(<arglist contents>)`

## Description

Parses an alias argument list and returns a standardized textual form. Supply the contents of the list without its enclosing parentheses. The function uses the same parser as [/alias](../commands/alias.md), but does not define an alias, bind arguments, or assign variables.

Argument descriptors are comma-separated. A named parameter defaults to `words 1`; serialization explicitly includes its word type and count, separates descriptors with `, `, and prints any default after the word count. Recognized modifiers and terminal markers are case-insensitive; parameter spelling is retained.

| Form | Meaning |
| --- | --- |
| `name [words count]` | Normal argument words, subject to the alias word mode. |
| `name uwords count` | Ordinary words without double-quote grouping. |
| `name dwords count` | Double-quoted words, with quotes removed when bound. |
| `name qwords count` | Double-quoted words, retaining quotes when bound. |
| `name default value` | Default text used when the argument is empty; a double-quoted default can be parsed as one value. |
| `...` | Stop parsing; leave remaining alias arguments in `$*`. |
| `void` | Stop parsing; discard remaining alias arguments. |

Word-type/count and default modifiers can occur on the same parameter. Without `...` or `void`, the final named parameter receives the remaining alias arguments when the resulting list is used. `$fix_arglist()` itself does not expand defaults; alias invocation does.

## Returns and restrictions

Returns the serialized argument-list contents, without parentheses. Empty input or only empty comma-separated terms returns the empty string. A terminal `...` or `void` ignores all later descriptors.

This function can normalize incomplete input, but is not a syntax validator and cannot guarantee that arbitrary input becomes a usable argument list. Unknown modifiers print a diagnostic when accompanied by a value; a modifier missing its value is discarded. Names are not validated, and counts are converted with `atol()` without rejecting zero, negative, or nonnumeric values.

Defaults are printed without restoring their surrounding quotes. A default containing spaces can therefore change meaning when the returned text is parsed again. Commas split descriptors even inside quoted defaults. Keep defaults simple if the output must be reused reliably.

The parser uses fixed storage without a bounds check. Supply at most 31 named parameters; larger lists can corrupt memory or crash EPIC.

## Examples

| Expression | Result |
| --- | --- |
| `$fix_arglist(channel, booya)` | `channel words 1, booya words 1` |
| `$fix_arglist(channel ooga, booya)` | `channel words 1, booya words 1`; the incomplete modifier is discarded. |
| `$fix_arglist(name default guest, ...)` | `name words 1 default guest, ...` |
| `$fix_arglist(void, ignored)` | `void` |

When generating an alias parameter list, escape its enclosing parentheses so their contents can expand:

```irc
alias oofda \($fix_arglist(channel ooga, booya)\) {
    echo $channel, $booya
}
```

The function call receives no enclosing argument-list parentheses; the escaped parentheses outside it become the alias's parameter-list delimiters.

## History

First appeared in EPIC5-0.0.3.

## Source

Registration and parse/serialize wrapper: [source/functions.c](../../source/functions.c). Argument-list parsing, serialization, and binding during alias calls: [source/alias.c](../../source/alias.c). Fixed argument-list storage: [include/alias.h](../../include/alias.h). Comma and quoted-word parsing: [source/ircaux.c](../../source/ircaux.c).
