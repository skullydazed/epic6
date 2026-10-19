# set show_status_all

## Synopsis

```text
/set show_status_all [on|off|toggle]
```

Default: `off`. With no argument, [set](../commands/set.md) displays the current value.

## Description

When on, allows status fields normally restricted to the current window to appear in other windows' status bars too. When off, those fields normally appear only in the input window of each screen. With multiple screens, “current” here is determined separately for each screen.

This affects the clock, notified-window list, insert/overwrite indicators, version, and ordinary user-defined status fields. User-mode, away, and operator indicators also use this restriction when EPIC is connected to exactly one server; with multiple connected servers, those indicators can already appear in noncurrent windows.

It does not add missing fields to a status format or override each field's other conditions. For example, the clock still requires [clock](clock.md) to be on. Some user-status variants explicitly display in all windows independently of this setting.

## Scope and effects

Changing the setting requests an update of all status bars. It does not create extra status bars, show hidden windows, change the selected window, or affect ordinary message output.

Not every status field uses this policy. Nickname formatting currently has no current-window restriction, so this setting does not control whether nicknames appear in noncurrent windows. Current-window markers and current/noncurrent status prefixes still test the actual current window; enabling this setting does not make all windows appear selected. Server-name display has its own rules as well.

Only fields present in the configured [status_format](status_format.md) or per-window formats can be shown. A field can still be empty because its underlying information or configured text is absent.

## Example

```text
/set show_status_all on
```

Visible noncurrent windows can now show applicable current-only fields from their own formats. Restore the usual restrictions with `/set show_status_all off`.

## Source references

- [source/vars.c](../../source/vars.c) and [include/config.h](../../include/config.h): boolean registration, status-update callback, and off default.
- [source/status.c](../../source/status.c): `IS_CURRENT_WINDOW` and `DISPLAY_ON_WINDOW` define the policy; individual status functions apply their own additional conditions. `status_nickname`, current-window markers, and prefixes use separate behavior.
