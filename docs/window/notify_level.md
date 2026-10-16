# window notify_level

## Synopsis

```text
/window [<window>] notify_level [<levels>]
/window [<window>] -notify_level
```

## Description

Choose which output levels can mark the selected hidden window [notified](notified.md). With no window selector, use the current window. If [window notify](notify.md) is on, the same transition can also produce an activity message in the global current window. Without a level argument, report the current mask.

Normal output must reach an invisible window, match this mask, and find its notified flag off before it marks the window notified, invokes `window_notified`, and requests status updates. With `notify` off, the flag and `%F` status listing can still change; that setting controls the activity message. Further matching output does not repeat the transition while the flag is already on.

Changing this mask does not clear an existing notified flag or retroactively examine stored output. It does not route messages, filter lastlog, hide output, or control the separate bell behavior of [window beep_always](beep_always.md). Notification processing can be suppressed for individual output, such as with [xecho](../commands/xecho.md) `-f`.

## Level list

Use the same level names as [window level](level.md), in one comma-delimited argument. Names are case-insensitive and accept prefixes; full names avoid ambiguous prefix matches. The list **replaces** the existing mask, starting empty:

- `msg,notice` selects private messages and notices.
- `all` selects all levels.
- `none` or `-all` clears the mask.
- Exclusions remove levels from the mask being built, as in `all,-joins,-parts`.

Unlike `window level`, this operation has no leading `+` incremental-add mode. A standalone exclusion does not subtract from the old mask; explicitly start with `all` when selecting all except certain levels.

Unsupported names produce a warning and a list of valid levels. Recognized parts still take effect, so a list containing only unsupported names leaves the mask empty. Additional whitespace-delimited words are processed as subsequent window operations, not as more levels.

## Defaults and output

New windows copy [set notify_level](../set/notify_level.md), whose compiled default is all. Changing that global setting changes the default for future windows and the current window's mask, not every existing window. `/window -notify_level` resets the selected window to the current global default.

Queries, changes, and resets report `Window notify level is <levels>`. The operation works on visible and invisible windows without changing visibility or input selection, and sends no IRC request.

## Examples

```text
/window 3 notify_level msg,notice
/window 3 notify on
/window 3 notify_level
/window 3 -notify_level
```

Limit window 3's qualifying output to private messages and notices, enable activity messages, report its mask, and restore its mask from the global default. Activity messages require window 3 to be hidden and its notified flag to be off.

## Source references

- [source/window.c](../../source/window.c): `windowcmd_notify_mask` implements `notify_level`, its query, and reset; `new_window` copies the default.
- [source/levels.c](../../source/levels.c): `str_to_mask` and `standard_level_warning` implement replacement-list parsing and warnings.
- [source/lastlog.c](../../source/lastlog.c): `set_notify_mask` updates the global default and current window.
- [source/screen.c](../../source/screen.c): `add_to_window` implements hidden-output matching and notification transitions.
- [include/config.h](../../include/config.h): defines the compiled notify-level default.
