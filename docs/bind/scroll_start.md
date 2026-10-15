# scroll_start

## Synopsis

```text
/bind <key> scroll_start
```

## Description

Move the current window's scrollback view toward the oldest retained output, entering scrollback when older output is available. This built-in action uses the same helper as [window scroll_start](../window/scroll_start.md).

If scrollback is already active, movement begins at its current position. Otherwise, it begins at the held view when that view is older than the normal scrolling view, or at the normal scrolling view. The action requests a window-body redraw and status update. At the oldest retained line, further invocations request a terminal beep and leave the view unchanged.

The window's scrollback display buffer lets you review previous output interactively. New output continues to be processed and added to that window's buffer while you browse older lines; processing of server messages is not paused. Other windows continue to display their own output. The action does not enable or disable hold mode.

Automatic scrollback trimming is suspended while the scrollback view is active, so a busy window left in scrollback can consume increasing memory. Lines already trimmed before entering scrollback cannot be recovered this way. Use [scroll_forward](scroll_forward.md) to review newer output, or [scroll_end](scroll_end.md) to advance toward the end and leave scrollback.

The action takes no arguments or options; extra text supplied with the binding is ignored. It does not change the input text or send an IRC request.

## Limitation

Each invocation moves backward at most 999,999 lines. An exceptionally large buffer may require repeated invocations to reach its oldest retained line.

## Default bindings and example

Defaults include Escape followed by `<` (`^[<`), `^[OH`, `^[[H`, `^[O1~`, and `^[[1~`. EPIC also binds the terminal's `key_home` sequence when available, normally the Home key. Scripts can change these bindings.

```text
/bind ^[< scroll_start
```

Press Escape followed by `<` to move toward the beginning of the current window's retained output.

See [bind](../commands/bind.md) for key sequence syntax and [scroll_backward](scroll_backward.md) to move backward by the usual page amount.

## Source references

- [source/keys.c](../../source/keys.c): action registration, default bindings, and terminal Home binding.
- [source/window.c](../../source/window.c): `scrollback_start`, `window_scrollback_start`, `window_scrollback_backwards`, and the line-count tester implement movement and view selection; `trim_scrollback` governs retention.
- [source/screen.c](../../source/screen.c): window redraw selects the appropriate normal, held, or scrollback view.
