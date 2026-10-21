# Status line

The status line displays information that changes with the client's state, such as your nickname, current channel, user and channel modes, query target, clock, and held output. Each window has its own displayed status, while many format settings are global defaults. Values come from client state and cached server information; displaying a field does not send a fresh IRC request.

## Customizing the line

[status_format](../set/status_format.md) selects the single-row layout. Literal text is copied, and case-sensitive percent fields insert status values. Common fields include:

| Field | Information |
| --- | --- |
| `%N` | Nickname. |
| `%C` | Current channel. |
| `%+` | Cached channel modes. |
| `%#` | User modes. |
| `%Q` | Query target. |
| `%R` | Window name, or refnum if unnamed. |
| `%T` | Clock, subject to clock and visibility settings. |
| `%U` | User-supplied text from `status_user`. |
| `%>` | Start of the right-aligned section. |
| `%%` | Literal percent sign. |

Many fields use a corresponding `status_*` setting as a wrapper. For example, [status_query](../set/status_query.md) controls the label and formatting around `%Q`. Other fields insert a setting directly. See [status_format](../set/status_format.md) for the full field list, map syntax, alignment, repeated fill, and limits.

[status_user](../set/status_user.md) supplies arbitrary text wherever the outer format contains `%U` or `%0`; changing that setting does not add a missing field to the layout. Numbered `status_user1` through `status_user39` provide additional slots. The string settings are global, even when displayed in different windows.

## Windows and visibility

[window status_format](../window/status_format.md) overrides the global single-row format for a particular window. With [window double](../window/double.md) enabled, the two rows instead use `status_format1` and `status_format2`, with matching per-window overrides. Editing the single-row format therefore need not change a double-row window.

Some fields normally appear only in each screen's input window. [show_status_all](../set/show_status_all.md) relaxes that restriction for applicable fields; other conditions still apply. A field can also be empty because there is no query, channel, server, or qualifying state. Check the field's own setting before treating missing text as a display failure.

## Example

A compact single-row layout:

```text
/window double off
/set status_format %R %N %C %Q %U %> %T
/set status_user Ready
```

This changes the global format; a per-window override can still take precedence. It replaces any formatting controls in the previous format, including reverse video. The space before `%>` also gives the renderer a space to use for fill.

## Dollar expandos and updates

Percent fields work independently of [status_does_expandos](../set/status_does_expandos.md). That setting, off by default, enables an additional dollar-expansion pass over the assembled line, using the displayed window and its server as context. Preserve dollar signs when assigning text intended for that later pass. This does not recursively interpret arbitrary percent fields inside user text.

Status values are regenerated when client activity requests an update, not by an independent evaluation loop for every custom function. Keep custom status functions inexpensive and avoid side effects. Long content is constrained by internal buffers and available screen width, so a format can be cropped rather than fully visible.

## Source references

- `source/status.c`: field registry, wrappers, user fields, format selection, contextual expansion, layout, and rendering.
- `source/window.c`: per-window overrides, status-row allocation, and double mode.
- `source/vars.c`: global status settings and update callbacks.
