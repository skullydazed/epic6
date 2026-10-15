# scroll_end

## Synopsis

```text
/bind <key> scroll_end
```

## Description

Move toward the end of the current window's scrollback and leave the scrollback view when it catches up with the normal or held view. This built-in action uses the same helper as [window scroll_end](../window/scroll_end.md).

If scrollback is active, the action advances that view. It does not turn hold mode off, so leaving scrollback can return you to a held view with newer output still waiting. If scrollback is not active but the held view is behind the normal scrolling view, the action advances the held view instead. When neither view can advance, it requests a terminal beep and makes no change.

Movement requests a window-body redraw and status update. The action does not change the input text or send an IRC request. It takes no arguments or options; extra text supplied with the binding is ignored.

## Limitation

Each invocation advances at most 999,999 lines. In an exceptionally large buffer, one invocation may leave the view short of its destination; invoke it again to continue. Once scrollback is inactive and no excess output remains held, normal automatic scrollback trimming can resume.

## Default bindings and example

Defaults include Escape followed by `>` (`^[>`), Escape followed by lowercase `e` (`^[e`), `^[OF`, `^[[F`, `^[O4~`, and `^[[4~`. EPIC also binds the terminal's `key_end` sequence when available, normally the End key. Scripts can change these bindings.

```text
/bind ^[e scroll_end
```

Press Escape followed by `e` after browsing older output to advance toward the end of scrollback.

See [bind](../commands/bind.md) for key sequence syntax, [scroll_backward](scroll_backward.md) to browse older output, and [scroll_forward](scroll_forward.md) to move forward by the usual page amount.

## Source references

- [source/keys.c](../../source/keys.c): action registration, default bindings, and terminal End binding.
- [source/window.c](../../source/window.c): `scrollback_end`, `window_scrollback_end`, `window_scrollback_forwards`, and the line-count tester implement movement and hold-view selection; `recalculate_window_cursor_and_display_ip` cancels caught-up scrollback; `trim_scrollback` governs retention.
