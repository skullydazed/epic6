# history

## Synopsis

```text
/load history
/history [<count>]
/!<index-or-pattern>
$historyctl(<operation> [<arguments>])
```

## Description

This script implements input-line history retention, recall, and browsing. It is normally loaded by [global](global.md), and loads [addset](addset.md) for its settings. History is stored in one client-wide `array.history`, shared across windows and servers, rather than in per-channel buffers. Each stored record contains a Unix timestamp followed by the input text.

With history enabled, its numbered `input` hook records nonempty ordinary submitted input. The normal Enter/`send_line` path invokes that hook; active line-input prompts take a separate callback path. Recall expressions beginning `/!` are excluded from ordinary capture. A `sendline` alias also records its arguments before invoking the C built-in. Other scripts' input hooks can affect which lines are captured or processed.

Stored text is not automatically executed when recalled. Recall puts it into the input line for review and submission. Indices start at `0` for the oldest entry; the newest entry has index `numitems(array.history) - 1`. Deletion, capacity trimming, and duplicate removal can change indices, so they are not permanent identifiers.

## Key bindings and browsing

The script creates these named actions:

| Action | Behavior |
| --- | --- |
| `backward_history` | Move toward older entries; when not browsing, begin near the newest entry. |
| `forward_history` | Move toward newer entries; with the default circular mode, starting outside browsing selects the oldest entry. |
| `erase_history` | Clear the input line and cancel the browsing pointer; it does not erase the stored history buffer. |
| `shove_to_history` | Store current input without executing it, then clear the line. |

It binds Control-P and common up-arrow sequences to backward history, Control-N and common down-arrow sequences to forward history, and Control-U to erase history. With a positive history capacity it binds Control-] to shove-to-history. These bindings can replace earlier assignments; use [bind](../commands/bind.md) to customize them.

Entering browsing with a nonempty input line saves that draft first. Edits made while already browsing are not automatically stored merely by moving again. Ordinary recorded input resets the browsing pointer. With circular mode on, moving beyond either end wraps; with it off, backward movement stops at the oldest entry, while forward movement past the newest clears input and leaves browsing mode.

## Settings

All are script-defined settings initialized when this script loads:

| Setting | Default | Effect |
| --- | --- | --- |
| `history` | `150` | Maximum retained entries. Reducing it trims the oldest records. Zero deletes the array, clears the browsing pointer, removes the ordinary capture hooks, and unbinds shove-to-history. |
| `history_circleq` | `on` | Wrap browsing at buffer ends. |
| `history_remove_dupes` | `off` | Before adding, remove entries matching the new text through wildcard matching. The name is `dupes`, not `dups`. |
| `history_persistent` | `off` | Install an exit hook to save history when enabled; remove it when disabled. |
| `history_save_file` | `~/.epic_history` | Default file for explicit read/save and exit saving. |
| `history_save_position` | `on` | A pattern recall updates the browsing pointer to its match; off leaves the pointer unchanged. |
| `history_timestamp` | `off` | Include each entry's local time-of-day in `/history` output. |

Duplicate removal is not strict byte-for-byte equality: it constructs wildcard masks from the new input and matches stored timestamp/text records case-insensitively. Wildcard characters in input can therefore remove more than literal duplicate entries.

Zero capacity disables normal input capture, but does not remove all navigation aliases or recall hooks. Direct helper calls and remaining bindings are not comprehensively guarded against adding records while disabled; use `historyctl(add ...)` when you need its explicit capacity check.

## Commands and recall

`/history` displays all stored entries oldest first. `/history N` displays the first N entries, not the N most recent ones. A zero, omitted, or larger-than-buffer count displays all; a negative count displays only the heading. Output includes each zero-based index and, optionally, its stored time.

`/!N` retrieves the entry at numeric index N into the input line or prints `No such history entry`. `/!pattern` selects the newest entry matching a prefix of either ordinary input or slash-prefixed command text. It is not an implicit substring search; include a leading `*` when desired. EPIC wildcard syntax and case-insensitive array matching apply. Failure prints `No match`.

Extra words after a pattern recall are appended with a separating space. In the numeric branch, the script concatenates those extra words directly without inserting a space. Neither branch first clears the input buffer itself; the ordinary interactive Enter path has already cleared the submitted recall expression before its hook runs. Numeric recall does not update `history.index`; pattern recall does so only when `history_save_position` is on.

## historyctl function

| Operation | Behavior and return |
| --- | --- |
| `add <text>` | Add through `history.add` and return `1` when capacity is nonzero, otherwise `0`. Empty text can still return `1` without adding anything. |
| `delete <index>` | Call `delitem`; the alias defines no success/error return value. |
| `get <index>` | Return entry text without its timestamp, or empty for an invalid index. |
| `index <index>` | Set the browsing pointer and return `1` for an existing record, otherwise `0`. This changes state, not just validates an index. |
| `read [<filename>]` | Append saved records from the specified/default file, trimming against a normal positive capacity. Return the opened handle after closing it; return empty on open failure. Reading stops at the first empty line. |
| `reset` | Delete the array and clear the browsing pointer; no explicit return status. |
| `save [<filename>]` | Append stored timestamp/text records to the specified/default file. Return the opened handle after closing it, or empty on open failure. |
| `set <capacity>` | Invoke `set history` and return `1`, even if the setting parser rejects the value. It does not replace an entry's text. |

Read/save return values are file handles, not entry counts or verified I/O-success reports. Writes and closes are unchecked. EPIC `open(... W)` means append, so repeated saves can duplicate records already in the file. An empty array also produces an invalid descending index range in the save loop and can write blank records, which stop a later read. The file is plain text containing the captured input and timestamps.

Although the source contains a conditional startup read, it sets `history_persistent` to off before that check during normal loading. Enabling persistence afterward installs exit saving but does not itself read previous history. Use `historyctl(read ...)` explicitly if wanted. Reloading can reset settings and alter bindings; it is not a state-preserving configuration query.

## Examples

```text
/set history 200
/history
/!0
/echo $historyctl(get 0)
/set history_persistent on
```

These adjust capacity, list entries, prepare the oldest entry for editing, display its text, and enable exit saving. Use the function interface from scripts when you need direct access without changing the input line.

## History

The history script first appeared in EPIC5-0.0.5.

## Source references

- [script/history](../../script/history): settings, capture/recall hooks, bindings, persistence, and `historyctl` operations.
- [source/input.c](../../source/input.c): interactive submission and prompt callback paths.
- [source/commands.c](../../source/commands.c): built-in `sendline` and input-editing command dispatch.
- [source/array.c](../../source/array.c): stored indices, matching, and array mutations.
- [source/functions.c](../../source/functions.c) and [source/files.c](../../source/files.c): append-mode `open`, line reads, and writes.
