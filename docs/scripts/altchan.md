# altchan

## Synopsis

```text
/load altchan
```

## Description

Installs Escape-prefixed bindings for selecting windows by number and cycling through hidden windows. Terminals that send Escape plus a character for Alt can use the corresponding Alt combinations; otherwise press Escape and then the character. Loading the script replaces those bindings and defines numeric command aliases `1` through `9`.

| Key sequence | Script action |
| --- | --- |
| Escape+`1` through Escape+`9` | `altchan.toggle` with the corresponding digit. `/1` through `/9` are equivalent aliases. |
| Escape+`0` | `altchan.toggle 10`. |
| Escape+`?` | Prompts for a window number, then uses the first entered word as the target. Empty input does nothing. |
| Escape+`-` | [window previous](../window/previous.md), cycling backward through eligible hidden windows. |
| Escape+`=` | [window next](../window/next.md), cycling forward through eligible hidden windows. |

The script uses `winnum` from the bundled `builtins` compatibility aliases. It has no script-specific settings or load arguments.

## Number selection

`altchan.toggle <number>` applies these branches in order:

1. If the requested number equals the current window's number, it tries that number plus ten. If absent, it reports an error and stays put.
2. Otherwise, if the request equals the last digit of the current window's number, it tries the current number plus ten, then falls back to the requested base number. If neither exists, it reports an error.
3. Otherwise, it selects the requested existing window or reports that it does not exist.

Thus Escape+`5` can move from 5 to 15 to 25, and from 25 back to 5 when 35 is absent. It does not search past a missing next increment. If 15 is absent while currently in 5, it reports that absence rather than wrapping. Zero or an empty value does nothing.

Escape+`0` requests **10**, not the last digit zero: it can move from 10 to 20, but from 20 it selects 10 rather than continuing to 30. Do not assume this key cycles every multiple of ten.

## Selection and limitations

Numbered selections use [window refnum_or_swap](../window/refnum_or_swap.md). A visible destination is selected; a hidden destination is swapped into the current window's place if the normal swap requirements permit. Existing channels, queries, contents, and server associations remain with their windows. A target can be on another server; this is not channel cycling.

The previous/next bindings use their own hidden-window eligibility rules, including skip and swappable flags. Numeric selection can also fail when a required swap participant is nonswappable. The script's existence check does not guarantee the subsequent swap succeeds.

The prompt accepts a first word but does not comprehensively validate it as a positive integer before the toggle arithmetic and comparisons. Use actual positive window refnums. Invoking these actions sends no IRC request and does not create missing windows.

## Example

```text
/load altchan
/altchan.toggle 5
```

From another numbered window, this selects window 5 if it exists. From window 5, it instead attempts window 15. Use [window refnum](../window/refnum.md) or `refnum_or_swap` directly when you want unconditional selection behavior without this toggle policy.

## History

`altchan` first appeared in EPIC4-1.1.1. Blackjac rewrote it for EPIC5-0.0.6; the current bundled version identifies itself as version 2 from 2007.

## Source references

- [script/altchan](../../script/altchan): bindings, numeric aliases, prompt, and ordered toggle branches.
- [script/builtins](../../script/builtins): `winnum` compatibility function.
- [source/window.c](../../source/window.c): refnum selection, swaps, and previous/next hidden-window operations.
