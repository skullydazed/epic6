# window pop

## Synopsis

```text
/window [<window>] pop
```

## Description

Consume saved window references from the window stack created by [window push](push.md). With no window selector, use the current window's screen stack; an explicit visible selector chooses that window's screen. Stacks belong to screens, not servers.

**The current implementation drains the entire stack, rather than stopping after the first existing window.** It removes entries from newest to oldest, discarding references to windows that no longer exist. Each entry is consumed even when it cannot change selection.

For each existing visible window, EPIC calls the show helper, making it current and its screen's input window if [window swappable](swappable.md) permits it. A window that has moved to another screen can therefore select that other screen. Existing hidden entries are not shown: the handler attempts to select their nonexistent screen, which has no effect. Do not rely on `pop` to restore a hidden window; use [window show](show.md) explicitly.

The last successful visible selection determines the resulting global current window. This can be the oldest saved window rather than the newest. No window contents are deleted, and no IRC request is sent. Saved references use internal window identity, so ordinary [window number](number.md) changes do not redirect them to a different window.

## Errors and subsequent operations

An invisible screen-reference window reports `Cannot pop the window stack from an invisible window` and leaves the stack unchanged. `/window -pop` is a no-op.

If the stack is empty, or the final processed entry refers to a missing window, EPIC reports `The window stack is empty!` and stops subsequent operations in the same `/window` command. Earlier entries may already have changed selection before that message.

If the final entry refers to an existing window, subsequent window operations target that entry's window, even if it is hidden or its selection failed. This target can differ from the global current window. An unswappable visible entry reports `Window <name-or-number> can't be made visible`, but processing continues through the stack.

There are no arguments or options specific to this operation. Additional words are processed as subsequent window operations when the handler returns an existing target. Successful processing normally produces no confirmation message, although selection hooks can produce output.

## Example

```text
/window 2 push
/window 3 push
/window pop
```

Assuming windows 2 and 3 remain visible, swappable, and on the same screen, `pop` consumes both entries: it selects window 3 first, then window 2. Window 2 becomes current and the stack is empty. If either window was hidden, that entry would be consumed without showing it.

## Source references

- [source/window.c](../../source/window.c): `windowcmd_push` stores internal window references; `windowcmd_pop` drains the stack and retains the final lookup result; `show_window` checks swappability and selects visible entries. `set_screens_current_window` returns immediately for a negative screen number, explaining the hidden-entry behavior.
