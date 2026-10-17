# on keybinding

## Synopsis

```text
/on [<noise>]keybinding "<pattern>" [(<argument list>)] { <commands> }
```

## Description

Triggered immediately before a resolved [key binding](../commands/bind.md) executes its action. It applies to both built-in actions and script-defined binding actions. It also runs when [parsekey](../commands/parsekey.md) invokes an action without a physical keypress.

This reports action dispatch rather than every byte received from the terminal. An incomplete or ambiguous key sequence can wait for resolution; an unresolved sequence can be decomposed into smaller bindings, producing events for the actions that actually execute. A key with no bound action does not directly generate an event.

Use standard [on](../commands/on.md) matching and noise controls. The pattern matches all three fields below together. There are no event-specific options.

## Parameters

| Parameter | Meaning |
| --- | --- |
| `$0` | Name of the binding action selected. |
| `$1` | Reserved sequence-length field, currently always `0`. |
| `$2` | Numeric value of the final key-sequence node, or `0` for `parsekey`. |

`$0` is the action name, not the textual key sequence. For an ASCII final byte, `$2` is its ASCII value. Internally the value is stored as a C `char` and converted to an integer, so values outside ASCII can depend on platform character signedness. It is not a full terminal key identifier or Unicode code point. Binding argument text is not included in this event.

## Default behavior and suppression

If the hook permits default handling, EPIC invokes the script alias associated with the action, if present, or otherwise its built-in function. The alias receives the binding's stored argument text; the built-in function receives the node value and stored arguments. The hook has no separate built-in display.

A suppressing hook, such as a serial-zero `^keybinding` hook, prevents that action from executing. This can suppress editing, submission, or other key behavior, so match the intended action rather than suppressing every binding indiscriminately.

The hook is not marked nonrecursive. Invoking `parsekey` inside it dispatches another keybinding event; avoid matching calls that repeat without terminating. It does not itself send anything to IRC; effects depend on the selected action or handler.

## Example

Observe beginning-of-line actions without replacing them:

```text
on -keybinding "beginning_of_line *" (action, length, keyvalue) {
    echo Binding $action activated with key value $keyvalue;
};
```

The quiet `-` noise mode preserves default action execution. For `/parsekey beginning_of_line`, the event's final field is `0`.

## Source references

- [source/keys.c](../../source/keys.c): `key_exec` invokes the hook before aliases or functions; sequence resolution and `key_exec_bt` handle incomplete sequences; `parsekeycmd` dispatches a synthetic node with value zero.
- [source/hook.c](../../source/hook.c): the `keybinding` registration permits recursion; `do_hook` implements matching and default suppression.
