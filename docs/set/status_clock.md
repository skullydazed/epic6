# set status_clock

## Synopsis

```text
/set status_clock [<text>]
/set -status_clock
```

Default: ` %T`, including a leading space. With no text, [set](../commands/set.md) displays the current value. Unsetting removes the wrapper and makes the field empty; it does not turn the clock timer off.

## Description

Sets the text used for the clock field in the status bar when [clock](clock.md) is on. When the clock is off, the field is empty.

The field is represented by `%T` in [status_format](status_format.md), or a per-window status format. The first `%T` inside `status_clock` is replaced with EPIC's cached clock text before insertion into the outer line. Other status percent fields are not recursively interpreted there. Omitting `%T` can display a fixed label instead of the time while the field is enabled.

With [show_status_all](show_status_all.md) off, the clock field is restricted to the input window of each screen. Turning that setting on permits it in other windows too. The appropriate status format must still contain `%T`.

## Time formatting and updates

This setting wraps the clock text; it does not define the time representation. [Clock_format](clock_format.md) selects the standard time format when set, otherwise [clock_24hour](clock_24hour.md) selects the standard 12- or 24-hour representation. [Metric_time](metric_time.md) selects millidays instead. [Clock_interval](clock_interval.md) controls automatic clock refresh timing.

Changing `status_clock` rebuilds status formats and requests updates. It does not itself update the cached time, change the interval, or send an IRC request. Ordinary command parsing can expand dollar expressions on assignment; [status_does_expandos](status_does_expandos.md) can additionally expand dollar expressions in the assembled status line.

Clock refreshes request automatic status updates only when the stored `status_clock` string is nonempty. Removing it can therefore also remove clock-driven status refreshes used by other dynamic fields. Use [clock](clock.md) off to hide the field and stop normal clock updates together.

## Example

Display the time in brackets:

```text
/set status_clock [%T]
```

Use `/set status_clock %T` for just the clock text without the default leading space. These forms still require `clock` on and `%T` in the outer format.

## Source references

- [source/vars.c](../../source/vars.c) and [include/config.h](../../include/config.h): string registration, status rebuild callback, and default text.
- [source/status.c](../../source/status.c): `%T` mapping, `convert_sub_format`, `status_clock` visibility tests, and optional final expansion.
- [source/clock.c](../../source/clock.c): cached clock formatting, timer updates, and the nonempty-wrapper condition for status refreshes.
