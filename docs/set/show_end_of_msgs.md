# set show_end_of_msgs

## Synopsis

```text
/set show_end_of_msgs [on|off|toggle]
```

Provided by the bundled `builtins` script. Default: `on` when that script initializes it. With no argument, [set](../commands/set.md) displays the current value.

## Description

Controls the script's suppression of selected server messages indicating that a command's output has completed. These markers can help distinguish a completed reply from one still arriving, including a request that returned no entries.

When off, the script's numeric hooks suppress default display for the selected replies. When on, those hooks permit normal handling; they do not force a message to appear if another hook or the built-in reply handler suppresses it.

The exact numeric list handled by this script is:

```text
219 232 262 315 318 347 349 365 366 368 369 374 394
```

This includes endings associated with WHO, WHOIS, NAMES, WHOWAS, LINKS, and several list queries. It is not a blanket switch for every completion numeric: for example, the script does not include LIST's `323` reply.

## Scope and limitations

This is a script-defined boolean, not a registered C setting. Without the script defining the setting and its hooks, it provides no built-in display policy. Replacing or removing those hooks can change its effect.

The setting does not prevent requests, discard result entries, or stop protocol processing. It changes handling of subsequent completion replies, not messages already displayed. Servers determine which replies they send, and EPIC's normal handlers can impose additional display conditions. For example, the built-in end-of-NAMES display is omitted while the channel is still synchronizing even when this setting is on.

## Example

Hide the script's selected completion markers:

```text
/set show_end_of_msgs off
```

Restore normal handling with `/set show_end_of_msgs on`.

## Source references

- [script/builtins](../../script/builtins): defines the boolean, initializes it to on, and installs conditional numeric hooks for the listed replies.
- [source/numbers.c](../../source/numbers.c): numeric processing and default displays, including end-of-NAMES conditions.
- [source/hook.c](../../source/hook.c): hook matching and conditional default suppression.
