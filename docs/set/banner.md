# set banner

## Synopsis

```text
/set banner [<text>]
/set -banner
```

Default: `***`. With no text, [set](../commands/set.md) displays the current value. `/set -banner` unsets the string; it does not restore the default. Text may contain spaces. Restore the default explicitly with `/set banner ***`.

## Description

Changes the default `***` prefix in front of informational messages. The banner is used by internal `say` messages, system-error diagnostics, many numeric displays, and [xecho -b](../commands/xecho.md). It is not a prefix automatically applied to every chat line or every script `echo`.

Set [banner_expand](banner_expand.md) on if the stored banner contains expandos that should be evaluated each time the banner is used. That setting defaults to off. Normal command parsing can already expand text while executing a `set` statement, so preserve literal expandos when storing a banner intended for later evaluation. Per-use expansion runs without message arguments; it does not receive the text being prefixed as `$*`.

Informational output normally inserts a space between the banner and message. Unsetting the banner removes its text but does not necessarily remove that separating space. Changing the setting affects future output, not text already in scrollback, and sends no IRC request.

## Numeric messages

Output that uses EPIC's numeric banner helper can substitute the current three-digit numeric when [show_numerics](show_numerics.md) is on. In that case the configured banner is not used for that prefix. The numeric helper also limits its returned prefix to 79 bytes; the ordinary informational-message path does not share that particular prefix limit.

`xecho -b` uses the same helper, so its prefix can also be a numeric while running in a numeric-processing context. Scripts that print their own prefixes or replace message formatting can bypass the banner entirely.

## Example

```text
/set banner [epic]
/xecho -b This is an informational line.
```

Outside a numeric context, this prints the line with `[epic]` as its prefix. Reset it with:

```text
/set banner ***
```

## Source references

- [source/output.c](../../source/output.c): `vsay` prefixes informational output; `vsyserr` uses the banner before `INFO --`.
- [source/numbers.c](../../source/numbers.c): `banner` selects numeric or configured prefixes and performs optional expansion.
- [source/commands.c](../../source/commands.c): `xechocmd` implements `-b` with the banner helper.
- [source/vars.c](../../source/vars.c): string setting registration and set/unset behavior.
- [include/config.h](../../include/config.h): default banner and disabled per-use expansion.
