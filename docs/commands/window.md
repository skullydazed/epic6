# window command

## Synopsis

```text
/window [window-reference] operation [operation-arguments ...] ...
/window
```

## About servers and screens

Windows hold IRC output and associate it with a server, channels, query targets, and output levels. Several windows can share one server. A window can also have no server. Attaching a window can initiate a server connection; removing the last window can close that connection when the server's autoclose policy is enabled. These are client associations, not additional IRC user sessions for each window.

A screen is the terminal display and input surface containing visible windows. Hidden windows have no screen but continue receiving routed output, maintaining history, and using their associated server. Hiding a window does not leave its channels or disconnect it.

## Syntax

With no arguments, `window` describes the current window. Otherwise, operations begin with the current **window**, not the current server. A window reference between operations selects the target for subsequent operations without necessarily making it the input window. Numeric references and window names are accepted; use `refnum` or `refnum_or_swap` to change focus explicitly.

Operations consume their own arguments and return the target for the next operation. Creation, switching, or deletion may change that target. Arguments are generally words; selected string operations accept a quoted word containing spaces. An unquoted `echo` consumes the whole remainder. Use complete operation names: the dispatcher matches case-insensitive prefixes in table order and selects the first match, rather than rejecting ambiguous abbreviations. Operation names take precedence over matching window names.

An invalid reference or operation stops further processing. A handler returning failure also stops the chain, but not every reported error does so; earlier successful changes are not rolled back. Each dispatched operation invokes the window_command hook afterward with old target number, resulting target number (or `-1`), and the supplied operation spelling.

A leading minus on an operation passes it no argument list. This is **not** a general undo or off switch. Many such forms do nothing; others clear or reset a property. For example, `-query` clears all query targets, `-name` unsets the name, `-lastlog_level` selects none, and `-notify` turns hidden notification off. Ordinary missing arguments can query a property, consume the next operation word as a value, or fail, depending on the operation. Put queries last and supply explicit values in chains.

## Server and routing operations

| Operation | Purpose and arguments |
| --- | --- |
| `server description` | Associate this window with an existing or newly defined server. Uses server descriptions understood by [`server`](server.md); connection changes are asynchronous. Without a description, report its server. |
| `noserv`, `discon` | Detach this window from its server and reassign its channels. |
| `channel channels` | Move already joined channels to this window, or request joins for new channels. Comma lists are accepted. Use a quoted argument such as `"#channel key"` for keys, or `-invite` for the last invitation. With no argument, list current/all associated channels. |
| `rejoin channels [keys]` | Request joins on this window's server, or move already joined channels here; accepts comma lists and `-invite`. `0` sends `JOIN 0`. |
| `claim channel` | Claim a pending joining channel for this window. |
| `add targets`, `remove targets` | Add or remove comma-separated message-routing targets. |
| `query targets` | Add comma-separated conversation targets and select them as query targets. Bare `query` removes the current query; `-query` removes all. See [`query`](query.md). |
| `level [+|-]levels` | Replace, add, or remove output levels routed here. Claiming levels removes them from other windows on the same server. No value displays the mask. |
| `echo "text"` | Display text in this window. Without quotes, all remaining text is consumed. |

Server joins and permissions remain server-dependent. `channel 0` is not a local detach form: use `rejoin 0` only when intending the server's leave-all-channels request.

## Screen management

| Operation | Purpose and arguments |
| --- | --- |
| `new` | Create a window on the target's screen and make it current. On a visible screen, this normally splits the available space. |
| `new_hide` | Create a hidden window; the chain retains its previous target. |
| `create` | Create another physical screen and window through the external wserv facility. |
| `delete`, `delete_kill` | Delete the target's secondary screen; the latter also attempts to remove its window. The main screen cannot be deleted. |
| `hide`, `hide_others` | Hide the target or other swappable windows on its screen. Remaining windows are resized. |
| `show reference`, `show_all` | Show a hidden window or attempt to show hidden windows on a screen. |
| `swap reference` | Exchange the target with a hidden window. |
| `refnum reference` | Select another window as current; a hidden selection is not automatically shown. |
| `refnum_or_swap reference` | Select a visible window or swap in a hidden one. |
| `goto position` | Select the numbered position on a screen, with 1 at the top. |
| `back`, `last` | Select a previously current window; `back` can swap a hidden window into view. |
| `next`, `previous` | Swap with a hidden window in reference-number order, wrapping and skipping ineligible windows. |
| `move offset`, `move_to position` | Change screen order relatively or absolutely. |
| `grow lines`, `shrink lines`, `size lines` | Resize the scrolling area. `size` without a number reports its height. |
| `balance` | Balance discretionary scrolling space among non-fixed windows on the screen. |
| `fixed on|off|toggle` | Control implicit resizing caused by changes to other windows. |
| `skip on|off|toggle` | Exclude the window from ordinary cycling and hidden-window next/previous selection. Explicit selection remains possible. |
| `swappable on|off|toggle` | Control eligibility for visibility changes. |
| `kill`, `killswap` | Destroy the target; `killswap` first swaps in a hidden window when possible. |
| `kill_all_hidden`, `kill_all_invisible` | Destroy eligible hidden windows; these are equivalent names. |
| `kill_others` | Current implementation selects the hidden list when the target is visible; it does not reliably destroy every other window. |
| `killable on|off|toggle` | Control eligibility for normal window destruction. |
| `merge reference` | Transfer history/channels and related log/timer references to a same-server window, then attempt to kill the source. Current target-list handling is incomplete; do not assume it safely preserves every conversation. |
| `push`, `pop`, `stack` | Maintain/display the screen's saved-window stack. `push` stores the target's internal reference. Current `pop` drains the stack rather than restoring just one entry and does not reliably show/select saved hidden windows. |

Visibility and resizing operations can fail when there is insufficient space, no eligible hidden window, fixed/swappable restrictions, or no usable screen. Window destruction discards its own buffers and reassigns associated channels; it does not promise to part those channels. It may also leave a server without windows and trigger autoclose.

`create` requires foreground operation, a configured `wserv_path`, a compatible wserv executable, and a supported terminal facility such as screen, tmux, or xterm. Selection depends on the environment and relevant wserv/terminal settings. It starts external processes and is not guaranteed on every terminal.

## View management

Normal scrolling, held output, and manual scrollback have separate positions. The display chooses among them, so changing one view does not necessarily replace a farther-back held or scrollback view.

| Operation | Purpose and arguments |
| --- | --- |
| `clear` | Move the normal view to the end, preserving stored lines. See [`clear`](clear.md). |
| `unclear` | Restore up to a current window-full without crossing the saved clear boundary, like [`unclear -noforce`](unclear.md). |
| `flush` | In hold mode, delete output below the held view and reset the normal view to it. Otherwise does nothing. |
| `flush_scrollback` | Delete the entire scrollback and clear the view. |
| `hold_mode on|off|toggle` | Enable or disable a held position; enabling starts from the normal view and advances it by the configured slider amount. |
| `hold_interval number` | Set a positive notification rounding interval; no value reports it. |
| `hold_slider percent` | Set held-view advancement from 0 through 100 percent; no value reports it. |
| `indent on|off|toggle` | Set this window's text indentation behavior. |
| `scrolladj on|off|toggle` | Bring recent normal-view lines back when the window grows. |
| `scrollback number` | Set the scrollback retention target, at least twice the display height. Held/viewed data can delay trimming. |
| `scroll_lines number` | Set normal scrolling step; `-1` uses the default, otherwise use a positive value within display height. |
| `scroll_backward [lines]`, `scroll_forward [lines]` | Move the scrollback view; absent or zero count uses the usual movement amount. Put these last if omitting the count. |
| `scroll_start`, `scroll_end` | Move to oldest scrollback or cancel the scrollback view. |
| `search_back [regex]`, `search_forward [regex]` | Search using a case-insensitive extended regular expression. A quoted pattern can contain spaces; omission reuses the client-wide last pattern. |
| `rebuild_scrollback` | Request a later rebuild from retained lastlog data. |

## Status, notification, and history

| Operation | Purpose and arguments |
| --- | --- |
| `double on|off|toggle` | Use one or two status lines. |
| `status_format`, `status_format1`, `status_format2` followed by `"format"` | Set window-specific single/double status formats. |
| `status_prefix_when_current`, `status_prefix_when_not_current` followed by `"text"` | Set current/noncurrent status prefixes. |
| `status_special "text"` | Set the window-specific status special text. |
| `notify on|off|toggle`, `notify_level levels`, `notify_name "text"` | Control hidden-window notification, qualifying levels, and displayed notification name. |
| `notified on|off|toggle` | Set or clear the existing notification flag. |
| `beep_always on|off|toggle` | Allow this window's beeps even when hidden, subject to beep settings. |
| `lastlog number`, `lastlog_level levels` | Set retained logical-output size and qualifying levels; lowering size trims history. |
| `clearlevel levels`, `clearregex "regex"` | Remove matching retained lastlog records. |
| `log on|off|toggle`, `logfile filename` | Control window file logging. A specified logfile is used directly; otherwise a suffix based on channel, query, or window number is appended to the global logfile setting. |
| `log_mangle "rules"`, `log_rewrite "text"` | Configure window log transformation and rewriting. |
| `name "name"`, `number number` | Set a unique nonnumeric name or change the user reference number. Renumbering to an occupied number exchanges user numbers. `name -` removes the name. |
| `topline number "text"`, `toplines count` | Set lines 1–9 above output and choose how many are visible, from 0–9. |
| `describe`, `list`, `lister "references"` | Show details, summarize all windows, or summarize selected windows. |
| `refresh` | Force pending status/window updates. |
| `check`, `screen_debug` | Diagnostic checks of history counts or screen/window state. |

Boolean values accept `on`, `off`, and `toggle`. Bare boolean operations generally report their value and stop the chain. String setters can unset their override when called with no string; they are not all read-only queries. Logging requires a writable file; changing the logfile while logging can close and reopen it. See [`lastlog`](lastlog.md), [`log`](log.md), and [`set`](set.md) for related facilities.

## Compatibility and examples

`scroll` and `scratch` remain accepted and parse a boolean but do not change window behavior. `bind`, `unbind`, and `prompt` are not in the current operation table. Unsupported operations report an invalid-window-or-option diagnostic.

Create and name a visible window, then join a channel there:

```text
/window new name chat channel #example
```

Target a named window without changing input focus, and display a quoted message:

```text
/window chat echo "Message in chat"
```

Clear that window or explicitly select it:

```text
/window chat clear
/window refnum_or_swap chat
```

Set per-window hold and inspect windows:

```text
/window hold_mode on
/window list
```

## History and source

`window` was present in ircII 2.1.5g; many operations are EPIC extensions. It is always registered. Local operations require no IRC connection, while joins and server operations do; physical display operations depend on fullscreen/screens. There is no function return value, and output depends on the selected operation.

[source/window.c](../../source/window.c) contains the full dispatch table, `windowcmd()`, operation handlers, views, routing, and server/window checks. [source/screen.c](../../source/screen.c) implements physical screens and wserv creation. [source/commands.c](../../source/commands.c) registers the command. [source/lastlog.c](../../source/lastlog.c), [source/log.c](../../source/log.c), [source/status.c](../../source/status.c), and [source/server.c](../../source/server.c) implement related history, file logging, status, and connections.
