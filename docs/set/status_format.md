# set status_format

## Synopsis

```text
/set status_format [<text>]
/set -status_format
```

Defines the appearance of the single-line status bar: time, window identity, channel and modes, query target, and other information. With no text, [set](../commands/set.md) displays the format. Unsetting removes its format text; it does not remove the window's status-row allocation.

The compiled default begins with a reverse-video control character followed by:

```text
%T [%R] %*%=%@%N%#%S%{1}H%H%B%Q%A%C%+%I%O%M%F%L %D %U %W
```

The retired `%L` and `%D` fields in that default insert nothing in EPIC6. Startup scripts can modify the format. Reverse video is not automatically applied to every custom format; include a reverse-video control character if wanted.

## Syntax of status expandos

Literal text is copied, and percent fields insert status values. Fields are case-sensitive even though command and setting names are not. `%C` and `%{0}C` are equivalent: `%{map}key` selects a numeric map and one character. `%%` inserts a literal percent sign. Unknown map/key combinations are consumed without a replacement.

The compiler accepts at most 40 recognized fields per line; later fields are skipped. Literal and expanded text also have internal buffer and screen-width limits. Keep formats compact rather than treating them as unlimited strings.

Some fields insert a setting directly. Others use a wrapper setting: the first corresponding percent field in that wrapper is replaced with the actual value. For example:

```text
/set status_query (Query: %Q)
```

Then `%Q` in the outer format supplies `(Query: alice)` while querying Alice. Wrappers do not recursively interpret arbitrary status fields. Dollar variables and function calls require [status_does_expandos](status_does_expandos.md); preserve dollar signs on assignment when deferring their evaluation.

## Standard fields

“Current” below means the input window of each screen, unless [show_status_all](show_status_all.md) is on. Other field-specific conditions still apply.

| Field | Value or controlling setting |
| --- | --- |
| `%A` | [status_away](status_away.md) when that window's server has an away message. Current-only when exactly one server is connected. |
| `%B` | Unseen/held row count through [status_hold_lines](status_hold_lines.md), rounded down to the window's hold interval; empty until a positive interval is reached. |
| `%C` | Current channel through [status_channel](status_channel.md), subject to private-channel hiding and channel-name width. |
| `%E` | Window's active activity-format expansion, if an activity is selected. |
| `%F` | Notified-window refnums through [status_notify](status_notify.md), current-only. |
| `%G` | Server's advertised NETWORK name, or `Unknown`. |
| `%H` | [status_hold](status_hold.md) when held or scrollback distance extends beyond display height. |
| `%I`, `%O` | [status_insert](status_insert.md) or [status_overwrite](status_overwrite.md), according to insert mode, current-only. |
| `%K` | [status_scrollback](status_scrollback.md) while browsing scrollback. |
| `%M` | [status_mail](status_mail.md) text directly. Mail checking and updates are supplied separately by scripts. |
| `%N` | Window's server nickname through [status_nickname](status_nickname.md); not current-only. |
| `%P` | Internal display-position diagnostics; not a stable script interface. |
| `%Q` | Query target through [status_query](status_query.md), when present. |
| `%R` | Window name, or user-visible refnum if unnamed. |
| `%S` | Server alias, falling back to configured server name, through [status_server](status_server.md). Hidden when exactly one server is connected. |
| `%T` | Cached clock through [status_clock](status_clock.md), when clock is on; current-only. |
| `%U`, `%X`, `%Y`, `%Z` | `status_user`, `status_user1`, `status_user2`, `status_user3`; current-only. |
| `%V` | EPIC version string, current-only. |
| `%W` | [status_window](status_window.md) on the actual current window when its screen has multiple visible windows. |
| `%#` | User modes through [status_umode](status_umode.md); current-only when exactly one server is connected. |
| `%*` | [status_oper](status_oper.md) for an IRC operator; current-only when exactly one server is connected. |
| `%+` | Current channel modes and arguments through [status_mode](status_mode.md), empty when no mode text exists. |
| `%.` | Per-window [status_special](../window/status_special.md) text. |
| `%=` | [status_voice](status_voice.md) when voiced but not a channel operator. |
| `%@` | [status_chanop](status_chanop.md), or [status_halfop](status_halfop.md) as a fallback. |
| `%\|` | `status_ssl_on` or `status_ssl_off`, according to the window's server connection. |
| `%>` | Starts the right-aligned section; see alignment below. |

## Additional maps

| Field | Difference from the standard field |
| --- | --- |
| `%{1}F` | Uses notify name, then window name, then refnum for notified windows; current-only. |
| `%{1}H` | [status_holdmode](status_holdmode.md) when hold mode is on and both held and scrollback distances are less than display height. |
| `%{1}K` | Scrollback position text `(Scroll: distance of total)` while in scrollback. |
| `%{1}P` | Current/noncurrent status prefix, using per-window overrides or global prefix settings. |
| `%{2}P` | Sequence-point diagnostic through `status_sequence_point`, only with sequence-point debugging enabled. |
| `%{1}R` | Numeric user-visible window refnum, even if named. |
| `%{1}S` | Server alias/name without the single-server hiding rule. |
| `%{2}S` | Full configured server name. |
| `%{3}S` | Server group, falling back to configured name. |
| `%{4}S` | Server-reported name, falling back to configured name. |
| `%{5}S` | Connection-state string, or `Unknown`; no `status_server` wrapper. |
| `%{1}T` | Literal `TEST`, not a clock. |
| `%{1}W` | [status_noswap](status_noswap.md) when the window is not swappable. |
| `%{2}W` | `status_window` on every window. |
| `%{3}W` | `status_window` on the actual current window, even with only one visible window. |
| `%{1}+` | Channel-mode wrapper even when its mode text is empty, provided the window has a server. |
| `%{2}+` | Mode letters without arguments; empty if no mode text exists. |
| `%{3}+` | Mode letters without arguments, allowing an empty mode value. |

Server fields return `No Server` when no server is associated, except that plain `%S` first applies its single-connected-server hiding rule. Current-window markers and prefixes use the actual input window, independent of `show_status_all`.

### User-defined fields

`%0` through `%9` insert `status_user` through `status_user9`. `%{1}0` through `%{1}9` insert `status_user10` through `status_user19`. These are current-only. Maps 2 and 3 similarly select `status_user20` through `status_user39`, but display independently of that restriction. `%U/%X/%Y/%Z` are aliases for the first four values, not separately configurable fields.

## Alignment and repeated fill

`%>` separates left and right sections. The right section is placed against the right edge when it fits; oversized content is cropped to the available width. The last printable character in the left section is used to fill remaining space, including the gap before a right section. End that section with a space, or enable [status_no_repeat](status_no_repeat.md), to use spaces instead. Formatting attributes do not themselves become fill characters.

## Double and per-window formats

With [window double](../window/double.md) on, the top and bottom status rows use [status_format1](status_format1.md) and [status_format2](status_format2.md), respectively, instead of this single-row format. Per-window [status_format](../window/status_format.md), `status_format1`, and `status_format2` override the matching global defaults. Changing a global format recompiles status lines and requests updates, while defined per-window overrides remain authoritative.

## Examples

A compact single-row format:

```text
/set status_format [%R] %=%@%N%C%+%Q%A %>%T
```

The space before `%>` supplies space fill, and the clock is right-aligned. Existing status text settings control the individual wrappers and separators.

A double-row configuration:

```text
/set status_format1 [%R] %N%C%+%Q%A %>%T
/set status_format2 %W %U %X %Y %Z 
/window double on
```

Fields can be empty because their conditions are unmet; these commands do not enable a clock, join channels, or create mail information.

## Source references

- [source/status.c](../../source/status.c): expando table, `build_status_format`, wrapper conversion, per-window inheritance, field implementations, contextual expansion, and width/fill layout.
- [source/vars.c](../../source/vars.c) and [include/config.h](../../include/config.h): registration, rebuild callback, and compiled formats.
- [source/window.c](../../source/window.c): per-window formats, status-row count, and window state.
