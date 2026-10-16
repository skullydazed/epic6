# window push

## Synopsis

```text
/window [<window>] push
```

## Description

Push the selected visible window onto its screen's saved window stack. With no window selector, save the current window. Each screen has its own stack, and the newest entry goes at the top. The same window can be pushed more than once; duplicate entries are not removed.

This saves a reference, not a copy of the window or its contents. It does not select, hide, resize, or move the window, and sends no IRC request. The stack stores internal window references, so changing a window's user-visible [number](number.md) does not redirect its saved entries to the window that later uses its old number.

Use [window stack](stack.md) to display saved entries and [window pop](pop.md) to consume them. In the current implementation, `pop` drains the entire screen stack and does not show hidden entries; it does not simply restore one saved window. See that operation's documentation before relying on it for a return-to-window workflow.

## Arguments and limits

`push` takes no refnum argument after the operation. To save a particular window, put its selector **before** `push`: `/window 3 push`. A number after `push` is processed as a subsequent window selector and does not change which window was just saved.

An invisible target reports `Cannot push an invisible window onto the window stack` and leaves the stack unchanged. `/window -push` is a no-op. There is no ordinary success message.

Additional words are processed as subsequent window operations on the same selected target, including after an invisible-window refusal. Pushing a window does not prevent it from later being hidden or destroyed.

## Examples

```text
/window push
/window 3 push
/window 3 stack
```

Save the current window, save visible window 3, and display the stack belonging to window 3's screen. If both pushes used the same window, it appears twice.

## Source references

- [source/window.c](../../source/window.c): `windowcmd_push` allocates a stack entry containing the selected window's internal refnum; `windowcmd_stack` displays entries; `windowcmd_pop` consumes the full stack. `windowcmd_number` changes user refnums without replacing internal window identity.
