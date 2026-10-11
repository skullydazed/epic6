# bindctl function

## Synopsis

```text
$bindctl(function name operation [arguments])
$bindctl(sequence keys operation [arguments])
$bindctl(map [keys [clear]])
```

`bindctl()` is a low-level interface to the keybinding system. A function here means a named binding action, not an ordinary EPIC function. A sequence is a series of keys that invokes an action. A map holds longer sequences beneath a common prefix. All operations act on the current keymap; they do not select a screen or alternate saved map and need no IRC connection.

Domain and action names are case-insensitive; key sequences are case-sensitive. `function`, `sequence`, and `map` accept their first letter as abbreviations. Missing required arguments or an unknown operation normally return the empty string. Function names and operations are parsed using the function word mode controlled by [`xdebug dword`](../commands/xdebug.md); sequence arguments always support double quotes.

## Working with functions

| Call | Effect and return value |
| --- | --- |
| `$bindctl(function name create text)` | Create a script-backed action, or replace an existing script-backed action; return `1` on success or `0` if a built-in would be replaced. |
| `$bindctl(function name destroy)` | Delete a script-backed action and remove keys using it from the current map; return `1`, or `0` for an absent or built-in action. |
| `$bindctl(function name exists)` | Return `1` if registered, otherwise `0`. The special `nothing` action always returns `1` here. |
| `$bindctl(function name get)` | Return `internal` for a built-in, `alias text` for a script-backed action, or empty if absent. |
| `$bindctl(function prefix match)` | Return action names beginning with the prefix. |
| `$bindctl(function pattern pmatch)` | Return action names matching a wildcard pattern. |
| `$bindctl(function name getpackage)` | Return its package tag, or empty if absent or untagged. |
| `$bindctl(function name setpackage package)` | Replace its tag with the entire remainder; return `1`, or `0` if absent. An empty remainder clears the tag. |

For this domain, `get` must be spelled exactly; `getpackage` otherwise accepts a leading `g`. Other operations accept their first letter, including `s` for `setpackage`. Use full names for clarity.

`create` stores the entire remaining text, except that a remainder beginning with a double quote is parsed as one quoted word and any following text is ignored. At invocation, stored sequence arguments are appended to that text, then the result runs as a command in a fresh `KEY` scope with an empty initial argument context. An alias name is a convenient choice; its existence is not checked at creation. Sequence arguments are appended command text, not supplied directly as the callback's `$*`. The normal keybinding hook can suppress execution.

Replacing a script-backed action first destroys its previous registration and unbinds keys using it. Rebind those sequences afterward. A newly created action acquires the current [`package`](../commands/package.md) tag, if any. Creating an empty script action is accepted but provides no useful command by itself.

## Working with sequences

| Call | Effect and return value |
| --- | --- |
| `$bindctl(sequence keys set action [arguments])` | Bind keys to an existing action, storing the entire remaining argument text; return `1` on success or `0` on failure. `nothing` removes the exact binding. |
| `$bindctl(sequence keys get)` | Return `action` followed by any stored arguments, or empty if unbound. |
| `$bindctl(sequence keys getpackage)` | Return the sequence's package tag, or empty if unavailable. |
| `$bindctl(sequence keys setpackage package)` | Replace the tag on a bound sequence. Success returns the empty string; an absent or unbound sequence returns `0`. An empty remainder clears the tag. |

`get` and `set` must be spelled exactly. Package operations require their first four characters, `getp` or `setp`. Binding a sequence acquires the current package tag and replaces any previous sequence tag. Package tags on actions and sequences are separate; [`unload`](../commands/unload.md) can remove tagged resources.

Sequences use the same notation as [`bind`](../commands/bind.md): for example, `^[g` means Escape followed by `g`, `^?` means Delete, and backslash-octal escapes or `\e` can describe keys. The keymap supports only bytes 0–127; sequences containing high-bit bytes are rejected, even if those bytes form valid UTF-8. Invalid/unknown actions can produce diagnostics as well as a `0` return. Removing an exact binding does not remove longer sequences under its prefix.

## Working with maps

`$bindctl(map)` returns a space-separated list of bound sequences recursively throughout the current map, including ordinary printable self-insert bindings. `$bindctl(map keys)` lists bound longer sequences beneath that prefix; it does not include the prefix's own binding. Returned keys use caret notation for control bytes. Listing skips NUL entries and their descendants, and does not quote sequences containing whitespace, so the result is not always an unambiguous word list.

`$bindctl(map keys clear)` removes the descendant map and all longer bindings under `keys`, preserving the exact prefix binding and action registrations. It returns `1` if a descendant map existed, or `0` otherwise. `clear` accepts its first letter. There is no clear-all operation using bare `map clear`: that treats `clear` as a sequence. Use valid, nonempty 7-bit sequence notation for map operations.

## Real example

Create a command alias and register an action that calls it:

```text
/alias greet echo Hi there!
@ result = bindctl(function greetings create greet)
@ result = bindctl(sequence ^[g set greetings)
```

Escape-g now calls `greet` under normal key dispatch. To remove the binding and action:

```text
@ result = bindctl(sequence ^[g set nothing)
@ result = bindctl(function greetings destroy)
```

## Source

[source/functions.c](../../source/functions.c) registers `bindctl` and delegates to [source/keys.c](../../source/keys.c), which implements operations, sequence parsing, maps, action invocation, and package cleanup.
