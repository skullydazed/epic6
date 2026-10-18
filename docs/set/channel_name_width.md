# set channel_name_width

## Synopsis

```text
/set channel_name_width [<number>]
```

Default: `0`. With no number, [set](../commands/set.md) displays the current value. The value must be a non-negative integer; negative or nonnumeric values are rejected.

## Description

Sets a limit on the channel-name text used by the standard status-bar channel field. A positive value truncates longer names to that many bytes; zero leaves the name unlimited by this setting. The status format around the channel field can add other text, so the setting is not a limit on the entire status bar.

The setting also affects built-in channel columns in several displays:

| Display | Positive value | Zero |
| --- | --- | --- |
| Status channel field | Truncates the channel text; does not pad it. | No limit from this setting. |
| [List](../commands/list.md) and standard [who](../commands/who.md) rows | Left-aligns and truncates the channel column to the requested width. | Uses the full channel name followed by a tab. |
| [Window list](../window/list.md) and related window listings | Sets the padded, truncated channel column width. | Uses a 12-byte channel column. |
| [Names](../commands/names.md) output | Has the special behavior described below. | Uses the full channel name followed by a tab. |

These are byte-oriented string operations, not Unicode display-column measurements. A limit can split a UTF-8 character or produce alignment that differs from terminal character widths. It does not shorten channel names stored internally or sent to the server, nor change names passed to event hooks.

Changing the setting requests status updates. Already printed lines are not reformatted; subsequent built-in output uses the setting. Script-provided formatting can use its own widths.

## Names output limitations

The initial channel-sync display, `Users on channel: ...`, does not apply this width. The separate `Pub`, `Prv`, and `Sec` display path does, but its current implementation does not consistently retain the padded format between replies: it constructs that format only when the configured width differs from the last observed width, and otherwise uses a tab-separated format.

In that path, a long public channel name is shortened and marked with a trailing `>` when the width is positive. Private and secret channel names do not use that explicit marker. The constructed field format also casts the width to an unsigned byte, so large values are not reliable for names-column formatting. Use modest widths rather than treating this as an unrestricted layout size.

## Example

```text
/set channel_name_width 16
```

Limit the ordinary status channel text and request a 16-byte channel column where supported. Restore the default behavior with:

```text
/set channel_name_width 0
```

## Source references

- [source/status.c](../../source/status.c): `status_channel` truncates the channel text before applying its status format.
- [source/numbers.c](../../source/numbers.c): list and names output use distinct formatting branches.
- [source/who.c](../../source/who.c): `whoreply` formats the channel column in standard WHO rows.
- [source/window.c](../../source/window.c): window-list formatting substitutes width 12 when the setting is zero.
- [source/vars.c](../../source/vars.c) and [include/config.h](../../include/config.h): integer validation, status-update callback, and default zero.
