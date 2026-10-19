# set show_who_hopcount

## Synopsis

```text
/set show_who_hopcount [on|off|toggle]
```

Provided by the bundled `builtins` script. Default: `on` when that script initializes the setting. With no argument, [set](../commands/set.md) displays the current value.

## Description

Intended to control whether the server hop count appears in [who](../commands/who.md) output. The current bundled implementation behaves opposite to that name: when on, its `on who` handler prints a replacement row that omits the hop count. When off, the handler permits normal WHO handling, whose built-in display includes the server's trailing hop-count/realname field.

The replacement displays channel, nickname, status flags, `user@host`, and the realname in parentheses. It uses `$7-` from [on who](../on/who.md), skipping the hop-count argument `$6`. The channel, nickname, and flags are width-formatted to 15, 9, and 3 columns respectively, using normal expansion padding and truncation rules.

## Hook interactions and limitations

This is a script-defined boolean and a script `who` hook, not a C formatting switch. The setting does not change the arguments supplied to the event, so other handlers can still inspect the hop count. However, the enabled replacement suppresses the normal display path, including its later numbered `352` hook. It is therefore not independent of WHO hook handling.

Other scripts can replace or alter that behavior. Queries consumed by a `who -line` callback, internal callbacks, or extended WHO reply handling do not necessarily reach this script hook. Server visibility rules and reply formats determine the data available; the hop count is server-provided information, not a network latency measurement.

Changing the setting affects subsequent eligible rows, does not request new WHO data, and does not alter already displayed text. Without the defining script and its hook, the name supplies no built-in formatting policy.

## Example

Allow the ordinary WHO path, including its hop-count field, with the current bundled handler:

```text
/set show_who_hopcount off
/who #example
```

Other hooks can still replace or suppress that output. `/set show_who_hopcount on` restores the script's default replacement format, which currently omits the count.

## Source references

- [script/builtins](../../script/builtins): defines the boolean, initializes it to on, and installs the conditional `who` hook using `$7-` rather than `$6-`.
- [source/who.c](../../source/who.c): `whoreply` constructs hook arguments, supports callback bypasses, and displays the hop-count/realname field when normal handling is permitted.
- [source/expr.c](../../source/expr.c): width-qualified expansion formatting used by the replacement row.
