# window number

## Synopsis

```text
/window [<window>] number <number>
```

## Description

Change the selected window's user-visible reference number to `<number>`. With no window selector, use the current window. Valid values are 1 through 1000. If another window already has that refnum, the two windows exchange refnums, even if they are on different screens or servers or one is hidden.

This changes identifiers, not window contents or positions. Names, channels, queries, server associations, visibility, input selection, and internal window identity stay with their original windows. Having channels does not prohibit renumbering. Use [window name](name.md) to assign a name instead, or [window move](move.md) and [window move_to](move_to.md) to change screen order.

EPIC requests status updates for the affected windows. `%R` shows the refnum only when a window has no name; `%{1}R` always shows the user-visible refnum. Changing refnums also changes the ordering used by [window next](next.md) and [window previous](previous.md), which select hidden windows by refnum. Scripts that save numeric refnums must account for their reassignment.

Subsequent operations in the same `/window` command still target the same selected window under its new number. No IRC request is sent, and successful renumbering normally produces no confirmation message.

## Arguments and errors

`<number>` is required. Without it, EPIC reports `Window number missing`; this is not a query. Values outside the accepted range leave the refnum unchanged and report `Window number must be between 0 and 1001`. That diagnostic gives the implementation boundaries, but the actual accepted range excludes both 0 and 1001.

Use ordinary decimal integers. The parser converts a numeric prefix rather than strictly validating the whole word, and interprets base notation such as a leading zero for octal or `0x` for hexadecimal. Nonnumeric input normally converts to zero and is rejected.

There are no additional options. Following words are processed as subsequent window operations, including after an invalid or missing-number diagnostic. `/window -number` is a no-op. Assigning a window its existing number leaves its identity unchanged and still requests a status update.

## Examples

```text
/window number 5
/window 3 number 7
/window list
```

Give the current window refnum 5, exchange window 3 with window 7 if 7 is occupied (otherwise assign 7), and inspect the resulting refnums.

## Source references

- [source/window.c](../../source/window.c): `windowcmd_number` exchanges user refnums and updates their lookup slots; `INTERNAL_REFNUM_CUTOVER` defines the exclusive upper boundary. `windowcmd_next` and `windowcmd_previous` use user refnums for hidden-window ordering.
- [source/ircaux.c](../../source/ircaux.c): `my_atol` performs base-aware numeric-prefix conversion.
- [source/status.c](../../source/status.c): `status_refnum` and `status_refnum_real` implement `%R` and `%{1}R`.
