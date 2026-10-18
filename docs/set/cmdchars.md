# set cmdchars

## Synopsis

```text
/set cmdchars [<characters>]
/set -cmdchars
```

Default: `/`. With no value, [set](../commands/set.md) displays the current string. `/set -cmdchars` unsets it. The setting remains a string variable, but its effect is limited as described below.

## Description

Historically this variable listed characters that could introduce commands on the input line, with slash always accepted. The current EPIC6 command parser instead recognizes `/` directly and does not consult `cmdchars` when deciding whether interactive input is a command. Setting additional characters does not enable them as built-in command prefixes.

Interactive input without a slash follows the ordinary text-send path to the current target. For example, setting `cmdchars` to `/!` does not make `!help` a command; do not rely on that spelling to invoke a local command.

## Remaining uses

The `$K` special expando returns the first byte of the configured string. When the setting is unset, it falls back to `/`. It does not return the complete set of characters, and setting a non-slash first character can therefore make script-generated command text disagree with the built-in parser.

Scripts can also read `$cmdchars` directly. The supplied `tabkey.ce` completion script uses it in its command-completion logic; that script behavior does not change the C command parser. Other scripts can implement their own input conventions, independently of the built-in parsing rule.

Changing or unsetting the string has no immediate callback and sends no IRC request. Because slash is the actual built-in command prefix, keeping the default avoids inconsistencies with scripts that still use this variable.

## Example

Restore the conventional value used by `$K` and scripts:

```text
/set cmdchars /
```

Use `/help` rather than an alternate prefix to invoke a local command under the built-in parser.

## Source references

- [source/vars.c](../../source/vars.c): retained string registration and standard query/unset handling.
- [include/config.h](../../include/config.h): default value `/`.
- [source/commands.c](../../source/commands.c): `parse_statement` recognizes literal slash and sends unprefixed interactive text to the current target.
- [source/functions.c](../../source/functions.c): `alias_cmdchar` implements `$K` from the first configured byte, falling back to the default when unset.
- [script/tabkey.ce](../../script/tabkey.ce): script-provided completion still reads `cmdchars`.
