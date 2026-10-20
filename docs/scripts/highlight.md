# highlight

## Synopsis

```text
/load highlight
/highlight
/highlight <pattern>
/nohighlight <pattern>
```

## Description

This script supplies local display highlighting using replacement event handlers. It was written to reproduce the former highlighted-ignore display style, but does not require or restore EPIC6's removed ignore subsystem. It highlights selected nicknames, userhosts, or destinations; it does not search arbitrary message bodies for keywords.

`highlight` without arguments displays `Highlight List:` and the stored patterns. With an argument it appends the first word to `highlights.list`; extra words are ignored and duplicates are not removed. Loading the script initializes the list to empty, so reloading loses existing patterns. The list is in memory, not automatically saved.

Patterns use case-insensitive EPIC wildcard matching, covering the whole field. For example, `Alice*` matches nicknames beginning with Alice and `*@example.test` matches userhost strings ending in that host. Nickname and `user@host` are tested separately, not as a combined `nick!user@host` mask. Channels and other destinations are tested only where the event handler supplies one.

For each pattern in list order, the helper checks nickname, then userhost, then destination. It returns after the first match, so it chooses one field to highlight rather than independently marking every matching field. The marker is the ordinary script variable `highlights.hl`, initialized to the bold control character. It is placed both before and after the selected field; existing formatting can affect the result.

## Removing patterns

`nohighlight` is intended to remove an exact stored pattern, not every pattern matching a wildcard. However, the current alias calls `remw` with the literal text `highlights.list` instead of the list's value. It therefore normally replaces the list with the single literal word `highlights.list` rather than removing the requested pattern. Do not rely on it for selective removal.

To clear the list deliberately without reloading the script, remove its ordinary assignment:

```text
/assign -highlights.list
```

Re-add the desired patterns afterward. This clears the list but leaves the replacement display hooks installed.

## Display facility and current limitations

The script installs suppressing serial-zero hooks for topic changes, public messages, group messages, private messages, signoffs, joins, invites, nickname changes, modes, kicks, and notices. They replace normal display even when no highlight pattern matches. It is not an additional styling pass that automatically preserves all of EPIC's default formatting.

| Event paths | Current behavior |
| --- | --- |
| `public`, `public_other`, `msg_group`, `msg`, `notice`, `public_notice`, `signoff` | Nickname matches normally highlight the printed nickname. A userhost match usually highlights that nickname too. Channel/destination highlighting is visible only when that field is actually printed. |
| `join`, `invite` | The printed nickname, userhost, or channel can be highlighted according to the selected match type. |
| `topic`, `public_msg` | The handlers receive `channel` but print an unrelated variable named `chan`. The channel in their output can consequently be empty or incorrect, including when channel highlighting was selected. |
| `nickname` (registered as `nick`) | Old and new nicknames can be highlighted. A matched userhost is modified but never printed. |
| `mode` | Nickname or target can be highlighted; a matched userhost is modified but never printed. |
| `kick` | Tests the victim and kicker nicknames and the channel; it supplies `*` in place of a userhost, so it does not perform a real host comparison. |

The source attempts `on wallops`, but the current event is [wallop](../on/wallop.md); `wallops` is not a recognized event name and that registration produces a diagnostic. No working wallop highlighting should be inferred from this file. Incoming actions have no handler in this script.

The private-message replacement calls an undefined `tia()` helper. This file does not provide a working timestamp implementation, and its formatting does not reliably reproduce the built-in away-message timestamp behavior.

Other scripts can replace or compete with these handlers under ordinary [on](../commands/on.md) rules. An earlier suppressing event can prevent a later specialized event from running, and a different handler that supplies its own output does not automatically use this script's highlighting. Incoming fields and event availability still depend on server messages and the client's cached state. No IRC request is sent just by adding or listing patterns.

## Example

```text
/load highlight
/highlight Alice*
/highlight *@example.test
/highlight
```

This installs the replacement displays, adds a nickname pattern and a separate userhost pattern, and lists them. Account for the removal and event-formatting bugs above before depending on the script in a customized configuration.

## History

This script first appeared in EPIC5-0.0.8.

## Source references

- [script/highlight](../../script/highlight): list commands, marker, match priority, event-specific fields, and retained bugs.
- [source/hook.c](../../source/hook.c): supported event names, prefix lookup, suppression, and hook dispatch.
- [source/functions.c](../../source/functions.c) and [source/reg.c](../../source/reg.c): `remw`, event userhost lookup, and wildcard matching.
- [source/parse.c](../../source/parse.c): incoming event fields and ordinary message classification.
