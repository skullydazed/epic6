# stack

## Synopsis

```text
/stack push|pop|list alias <name>
/stack push|pop|list assign <name>
/stack push|pop|list set <name>
/stack push|pop|list on <event-type>
/stack push|pop bind <key-sequence>
/stack list bind
```

## Description

`stack` saves aliases, global script assignments, settings, key bindings, or hook lists for later restoration. This lets a script temporarily replace an item, then restore the saved definition or value, overwriting the current one.

`push` saves the current item. `pop` restores and removes the most recently saved item for that name, event type, or key sequence. `list` displays saved entries without restoring them, newest first. Stacks for different names and kinds are independent: you can save several items and restore one without first restoring the others.

## Item types

| Type | What is saved | What happens on push |
| --- | --- | --- |
| `alias` | A user alias's body, stub information, and package information. | The live alias remains available. |
| `assign` | A global script assignment's value, stub information, and package information. | The live assignment remains available. |
| `set` | A built-in setting's type, current and original values, and script callback. | The setting remains unchanged. |
| `on` | The entire installed handler list for one event type, including its serial-number groups. | The handlers are removed from active dispatch until restored. |
| `bind` | One sequence's bound action, extra arguments, package information, and changed flag. | The binding remains active. |

Use [alias](alias.md), [assign](assign.md), [set](set.md), [on](on.md), and [bind](bind.md) to make temporary changes after saving an item. A pop replaces the current saved component; it does not merge the replacement with your temporary definition. For hooks, the restored handler list replaces the currently active list for that event. An implied hook is not part of the saved list and can still run after `stack push on`.

An absent alias or assignment can be saved as a placeholder, so a later pop restores its absence. An unbound key sequence can likewise be saved and restored. This is useful when the temporary item did not exist beforehand.

## Names, lists, and restrictions

Operation and type names are case-insensitive and accept prefixes, using the first matching operation or type. For example, `p` selects `push`, and `a` selects `alias`; use full names to avoid confusion.

Alias, assignment, and setting names are case-insensitive and support normal bracket-to-dot name conversion. Supply the exact name, not a prefix, expanded value, or list of names. The remaining argument text is treated as that name. `stack assign` operates on global assignments; it does not save or restore a visible [local](local.md) variable, and `:name` is not a local-stack selector here.

For `on`, supply one named or numeric event type; normal event-name lookup applies. It saves every handler for that type, rather than one pattern or serial number. For `bind`, use the same control-key and escape notation as `bind`. Only one key sequence is saved, not an entire keymap or a custom action's definition. Push reads the current keymap, and pop writes the current keymap at restoration time; the saved record does not select its original keymap.

`list alias`, `list assign`, and `list set` require a name and show that item's saved entries. They are not whole-registry listings. `list on` requires an event type and groups its saved lists by stack level. `list bind` shows all saved binding records, ignoring any supplied sequence filter. Missing saved entries normally produce a diagnostic; ordinary successful pushes and pops are generally silent. There is no script return value, automatic pop on script return, or persistence across client restarts.

Two restoration limitations apply in the current implementation:

- An alias's argument declaration is copied when pushed, but not restored by pop. Do not rely on this command to undo argument-list changes.
- Setting restoration searches EPIC's built-in setting registry. Script-created settings registered through `symbolctl` are not restored by this path, even though they can be saved and listed. Use a built-in setting's full name.

Restoring a built-in setting invokes its C and script callbacks, when present, and can change associated client state. It does not dispatch the normal `on set` request hooks. Saving a setting does not revert it to its default, and a later callback can normalize the restored value. Pushing a stubbed alias or assignment preserves the stub without loading it; listing a saved symbol uses normal lookup and can load a live stub.

## Examples

Temporarily change a built-in setting:

```text
stack push set beep
set beep off
stack list set beep
stack pop set beep
```

Temporarily remove all `who` handlers and restore them afterward:

```text
stack push on who
stack list on who
stack pop on who
```

Save and restore a global assignment:

```text
assign example original
stack push assign example
assign example temporary
stack pop assign example
echo $example
```

This displays `original`, provided a local `example` does not shadow that global assignment.

## Source

Registration, operation parsing, and type dispatch are in [source/commands.c](../../source/commands.c). Alias, assignment, and setting stacks are in [source/alias.c](../../source/alias.c); setting cloning and callback restoration are in [source/vars.c](../../source/vars.c). Hook-list ownership and restoration are in [source/hook.c](../../source/hook.c), and saved bindings and keymap restoration are in [source/keys.c](../../source/keys.c).
