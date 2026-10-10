# on

Register, list, or remove event handlers, also called hooks.

## Synopsis

```text
/on [#][noise]event [serial] [pattern-prefix]pattern [(arglist)] [{commands}]
/on
/on event
/on event -
/on #event serial -pattern
```

## Event-driven programming

EPIC raises events when something happens, such as a message arriving or someone joining a channel. Each event has a type and a data string. `on` selects events by type and wildcard pattern, then runs script commands with the event data available as `$*`, `$0`, `$1`, and so on. The data fields depend on the event. The optional argument list binds them as it does for [alias](alias.md).

A hook is uniquely identified by its event type, serial number, and case-insensitive pattern. Defining the same combination again replaces its attributes and action. Successful definitions print the event, pattern, action, noise level, serial number, and a separate hook identifier. The latter is used by `$hookctl()` and is distinct from the ordering serial.

## Event types and serial numbers

Event names are case-insensitive; an unambiguous abbreviation is accepted. `$hookctl(list lists *)` lists available types. Numeric IRC replies can be hooked by number, such as `433`. An unknown or ambiguous registration type produces a diagnostic.

The default serial number is zero. To specify another, put `#` first in the event token, before any noise modifier, and supply the serial immediately after that token:

```text
/on #-msg 500 "*" {commands}
```

Negative serials run before zero and positive serials after zero. A serial token of `+` or `-` chooses an unused positive or negative serial for that event type. Supply valid integer serials otherwise: the parser uses integer conversion rather than rejecting malformed tokens, and missing or misplaced serials can misparse the pattern.

For each serial number, EPIC selects the matching pattern with the highest wildcard-match score and executes at most one winning action. Ties follow the stored pattern order, so do not rely on definition order to break them. A broad handler and a more specific handler at the same serial are alternatives, rather than two actions that both run.

## Noise modifiers and default actions

| Modifier | Name | Ordinary command display | Activation alert | Suppress default action at serial zero |
| --- | --- | --- | --- | --- |
| `^` | Silent | Off | No | Yes |
| `-` | Quiet | Off | No | No |
| none | Normal | Off | Yes | No |
| `+` | Noisy | On | Yes | No |
| `?` | Unknown | Off | No | Depends on return value |
| `%` | System | On | No | Yes |

For `?` at serial zero, the action runs as a function: use [return](return.md) to return a numeric value. A value whose integer conversion is nonzero suppresses the default; zero, an empty result, or nonnumeric text permits it. At other serials, `?` runs as an ordinary command handler and does not make this suppression decision.

Ordinary noise-based suppression applies only at serial zero. The caller determines what honoring suppression means: it often suppresses display, but some events have other default actions or do not honor the hook result. Suppressing a notification does not inherently cancel the underlying IRC event. Commands such as [echo](echo.md) can explicitly display output even when ordinary command display is off.

All selected handlers run before the caller's default action. A positive serial therefore does not place your output after the default output. To control that order, replace the default with your own serial-zero display handler. An implied hook configured through `$hookctl()` also runs after ordinary handlers when the event remains unsuppressed; it displays its formatted result and suppresses the caller's default.

## Patterns and actions

Patterns match the full event data using EPIC wildcards, rather than regular expressions. `*` matches any sequence and `%` matches within a word; quote patterns containing spaces. An unquoted or double-quoted pattern is stored as a static pattern. A single-quoted pattern is flexible: it is expanded against the event data each time matching occurs. Normal surrounding script expansion still matters when defining a hook; flexible-pattern evaluation order relative to other candidates is unspecified. See [assign](assign.md) and [alias](alias.md) for variables and expansion.

A prefix immediately before the pattern changes its meaning:

- `-pattern` removes the exact stored pattern at the selected serial; it is not a wildcard search for hooks to delete.
- `^pattern` installs an empty action and ignores any supplied action or argument list. Its noise modifier still determines default suppression.
- `!pattern` installs an exception: when it wins, no action runs and that serial does not suppress the default. Other serials still participate. It does not fall back to the next-best match at its own serial.

The noise caret before the event and the empty-action caret before the pattern serve different purposes. Use braces around a command block, especially when it contains multiple statements. Without braces, the remaining text is the action; with no action, an empty handler is installed. Empty patterns, unmatched argument-list parentheses, and unmatched action braces produce diagnostics.

## Listing and deletion

`/on` lists all defined hooks. `/on event` lists that event's hooks; an abbreviated ambiguous type without a pattern can list the matching named event lists. `/on 0` lists all numeric hooks. Listings report empty lists where applicable.

`/on event -` removes every hook for that type. `/on #event 500 -` removes all hooks at serial 500. **A patternless removal at serial zero, including `/on #event 0 -`, removes the entire event list**, rather than only its serial-zero hooks. To remove one handler, supply its exact pattern and serial:

```text
/on #msg -100 -"* *"
```

## Examples

Display an additional message while permitting the usual default output:

```text
/on msg "*" {echo ..msg from $0:.. $1-}
```

Replace the default message display:

```text
/on ^msg "*" {echo ..msg from $0:.. $1-}
```

Record when a private message arrives without changing its default display:

```text
/on #-msg 500 "*" {@ last_msg_time[$encode($0)] = time()}
```

Delete those serial-500 message hooks:

```text
/on #msg 500 -
```

## Restrictions and source

The built-in is always registered. Event availability and data are determined by the code that raises each event. Some event types prevent recursive invocation; `$hookctl()` can also alter hook state and processing. Keep automated replies from creating message loops.

Registration is in [source/commands.c](../../source/commands.c). `oncmd`, hook identity, serial ordering, noise modes, matching, dispatch, implied hooks, and listing/removal are in [source/hook.c](../../source/hook.c). Wildcard matching is in [source/reg.c](../../source/reg.c); argument lists and command/function scopes are in [source/alias.c](../../source/alias.c).
