# hookctl function

## Synopsis

`$hookctl(<operation> [<arguments> ...])`

## Technical

Inspects and controls [on](../commands/on.md) hooks, event types, noise levels, and the current event dispatch. A hook's identifier (`refnum` below) is distinct from its ordering serial number. Identifiers are reusable after removal; they are not permanent handles.

Operation and property names are case-insensitive and accept prefixes, with the first matching keyword winning. Use full names to avoid ambiguity. Event types accept exact names, unambiguous prefixes, or numeric reply types. Most argument words use ordinary function parsing, whose quote handling follows `/xdebug dword`; trailing text fields consume the remaining input literally.

## Adding and removing hooks

`$hookctl(add [#][!][']<noise><type> [<serial>] <pattern> <commands>)`

The optional `#` introduces a serial (default zero), `!` creates an exception hook, and `'` makes the pattern flexible. Noise markers are `?`, `^`, `-`, `+`, `%`, or absent for normal noise. These prefixes belong to the type token. A serial of `+` or `-` selects an unused positive or negative serial for that event type. Other serials use decimal integer conversion.

`add` returns the hook identifier. An existing hook with the same event, serial, and case-insensitive pattern is replaced while retaining its identifier. The current package is recorded. Missing type, serial, or pattern can return `-1`; an invalid event returns empty.

Unlike `/on`, `add` does not parse an argument list or strip body braces: everything after the pattern is stored as command text. Set `argument_list` separately. A pattern containing spaces needs quote-aware function parsing. Flexible patterns are stored in uppercase and expanded when matching; preserve variable spelling accordingly.

`$hookctl(remove <refnum>)` removes one hook and returns `1`, or `-1` for a missing/invalid identifier. `-1` denotes the currently executing hook when one exists.

## Hook properties

`$hookctl(get hook <refnum> <property>)`

`$hookctl(set hook <refnum> <property> [<value>])`

The word `hook` may be omitted before a numeric identifier. Identifier `-1` selects the current hook. `get` without a property returns its command text. Invalid identifiers or properties return empty.

| Property | `get` result | `set` behavior |
| --- | --- | --- |
| `argument_list` | Normalized argument list, or empty. | Parses the remaining list without outer parentheses; returns normalized text. Empty clears it and returns empty. |
| `flexible` | `0` or `1`. | Nonzero decimal value enables flexible patterns; returns `1`. |
| `nick` | Stored wildcard pattern. | Uppercases and replaces the entire remaining pattern; returns `1`, or `0` for a duplicate event/serial/pattern. |
| `not` | `0` or `1` for an exception hook. | Nonzero decimal value enables it; returns `1`. |
| `noise`, `noisy` | Noise name. | Accepts an exact noise name or zero-based numeric ID; returns `1`, or `0` if invalid. |
| `package` | Package tag, or empty. | Replaces the entire remaining tag; returns `1`. |
| `serial` | Ordering serial. | Decimal conversion; reorders the hook and returns `1`, or `0` for a duplicate. |
| `skip` | `0` or `1`. | Nonzero decimal value marks the hook to be skipped; returns `1`. |
| `stuff` | Command text. | Replaces the entire remaining text verbatim; returns `1`. |
| `type` | Event name. | Read only; returns empty. |
| `string` | Text beginning with `ON` describing a definition. | Returns the same text; does not change the hook. |

Duplicate checks for `nick` and `serial` include the hook itself: setting either to its existing value returns `0`. Other omitted scalar values generally convert to zero; omitted text clears it.

`string` is not a lossless save format. It omits package and skip state, emits `^` rather than the exception pattern marker for negative hooks, and does not escape embedded pattern quotes. Review it before using it with [eval](../commands/eval.md) or saving it for later loading.

## Event lists and searches

| Operation | Result |
| --- | --- |
| `list [lists [<pattern>]]` | Event names; no subtype defaults to `lists`. |
| `list populated_lists [<pattern>]` | Event names having registered hooks, excluding implied-only events. |
| `list hooks [<pattern>]` | Hook identifiers for matching event names. |
| `count [lists\|populated_lists\|hooks [<pattern>]]` | Count of the corresponding list. |
| `number_of_lists` | Number of supported event types, including numeric types. |
| `first_named_hook` | Zero-based position of the first named event in `list lists`. |
| `serial <number> [<type>]` | Hook identifiers with that serial, across all types or one selected type. |
| `lookup <type> <pattern> [<serial>]` | Identifier matching the stored pattern literally and case-insensitively at serial zero by default; `-1` if absent. Invalid/missing arguments return empty. |
| `matches ...` | Empty; not implemented. |

List patterns are one function word and match complete event names with EPIC wildcards. Lists follow event registry order (numeric events first), then stored hook order. Searches do not execute hook bodies.

**The top-level `package` and `populated_lists` keywords are miswired in the current implementation.** `populated_lists <package> [<type>]` actually returns identifiers tagged with that exact case-insensitive package; top-level `package` reaches an unsupported branch, prints a diagnostic, and returns empty. Use `list populated_lists` for the populated event list. Without a type, the miswired package search and `serial` return identifiers in ascending identifier order.

`$hookctl(match <type> <text>)` attempts to select matching hook identifiers without running their bodies. Its text is the entire remainder. It expands flexible patterns with empty event arguments, ignores skip state and global hook denial, and carries its best score across serial groups instead of resetting it. Results therefore do not reliably predict which hooks actual dispatch will execute. A winning exception suppresses a result; a recursively blocked event returns empty.

## Event properties and implied hooks

`$hookctl(get list <type> [<property>])`

Properties are `name`, `params` (also `parameters` or `parametres`), `mark`, `flags`, and `implied`. `params` is the event's pattern-field metadata; `mark` is its current invocation depth and is the default property. Flag bit `1` prevents recursive dispatch. Type `-1` selects the current hook's event; avoid this form after removing that current hook.

`$hookctl(set list <type> implied [<format>])` sets an implied display format and returns `1`; an empty format clears it. Other list properties are queries even when requested with `set`.

An implied format runs after ordinary hooks when default handling remains unsuppressed, even if the event has ordinary hooks. It formats the event arguments through [$cparse()](cparse.md), displays the result, and suppresses default handling. Balanced outer braces are removed and select protected formatting without the additional alias-expansion pass. Unbraced formatting initially includes that extra expansion pass. Once protected formatting has been selected for an event, later unbraced assignments do not reset the protection flag.

`current_implied_hook` returns the event name while an implied format is being evaluated, otherwise empty. This is separate from the ordinary executing-hook stack.

## Noise types

`noise_levels [<pattern>]` lists `UNKNOWN SILENT QUIET NORMAL NOISY SYSTEM`, optionally filtered by a one-word wildcard pattern. IDs are `0` through `5` in that order. `noise_level_num` returns their count (`6`), and `default_noise_level` returns `NORMAL`.

`$hookctl(get noise <name-or-id> <property>)` (also `noisy`) supports `name`, `value` (the numeric ID), `display`, `alert`, `suppress`, `identifier`, and `custom`. Noise names must be exact. `identifier` returns the numeric character code, such as `94` for silent's `^`, rather than the character itself. `custom` is zero for all current types. `set noise` is also a query; it does not alter noise definitions.

`display` reports whether ordinary command display is enabled while that noise's hook runs; `alert` reports activation announcements. `suppress` returns a Boolean, so both silent/system suppression and unknown's conditional suppression report `1`. Actual suppression applies at serial zero and depends on the event caller; see [on](../commands/on.md).

## Current dispatch and miscellaneous operations

| Operation | Effect or result |
| --- | --- |
| `executing_hooks` | Identifier stack, current frame first; empty outside dispatch. A frame can contain `-1` before a concrete hook is selected. |
| `haltchain [<depth>]` | Stops further hook selection in the selected event frame; `1` on success, `0` if no frame. Default depth zero. Does not stop the current body or inherently suppress default/implied handling. |
| `args <depth> [<text>]` | Replaces the selected frame's event text for subsequent matching and returned event arguments; `1` on success, `0` if no frame. Does not rewrite the already running body's `$*`. |
| `userinfo [<depth> [<text>]]` | Gets per-frame script metadata, or stores remaining nonempty text and returns `1`. Default depth zero; no frame returns `0`. Empty text queries rather than clears. |
| `retval [<value>]` | Gets or sets the current event result; empty outside dispatch. Valid setters return `1`, invalid values `0`. |
| `deny_all_hooks [<number>]` | Queries or sets global hook denial; nonzero enables it, and the resulting `0`/`1` is returned. Future ordinary and implied dispatches are denied; an already running body continues. |
| `hooklist_size` | Allocated identifier slot count, not active hook count. |
| `empty_slots` | Unused identifiers within those allocated slots. |
| `last_created_hook` | Most recently added/replaced hook identifier; it may subsequently have been removed. |

Depth is the number of enclosing event frames to skip, not a hook identifier. Supply a nonnegative depth. For `args`, omitting all arguments clears the current frame's text; when replacing text, explicitly supply the depth first.

`retval` values are `-1`/`no_action_taken`, `0`/`suppress_default`, `1`/`dont_suppress_default`, and `2`/`result_pending`. Later hooks may change the result. For a pending result, dispatch runs the selected body as a function and determines suppression from its returned integer. Caller behavior determines which default action is affected.

## Examples

```irc
@ hook_id = hookctl(add ^msg * echo Message hook fired)
@ hookctl(set hook $hook_id argument_list nick, msg)
@ hookctl(set hook $hook_id stuff echo Message hook updated)
@ pattern = hookctl(get hook $hook_id nick)
@ hookctl(remove $hook_id)
```

Use `$hookctl(list hooks msg)` to inspect message-hook identifiers or `$hookctl(list lists g*)` to find event names beginning with `g`.

## Source

Registration and wrapper: [source/functions.c](../../source/functions.c). All operations, hook storage, noise definitions, matching, implied formatting, and event frames: [source/hook.c](../../source/hook.c). Argument-list parsing: [source/alias.c](../../source/alias.c). Prefix matching: [source/ircaux.c](../../source/ircaux.c).
