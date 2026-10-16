# window prompt (removed)

## Former synopsis

```text
/window [<window>] prompt <text>
```

## Description

The former `window prompt` operation set a window-specific input prompt associated with process input and the `$T` target used in [set input_prompt](../set/input_prompt.md). EPIC6 does not register or implement this window operation. The old automatic process-prompt behavior is not available through it.

In an unmodified session, `/window prompt text` reports `WINDOW: Invalid window or option: [prompt]` and stops processing that command. Since unrecognized operation words are also tried as window names, a window actually named `prompt` can instead be selected; that does not restore the removed operation. A script-defined replacement would have its own behavior.

## Current prompt control

Use [set input_prompt](../set/input_prompt.md) to configure the normal input-line prompt. Its value is expanded when the input line is redrawn. This is global prompt configuration, not the former per-window process-prompt override. `$T` now comes from the current window's target rather than from a `window prompt` value.

```text
/set input_prompt IRC>
```

Set a simple normal input prompt. The removed operation has no supported arguments or defaults in EPIC6.

## Source references

- [source/window.c](../../source/window.c): the window operation table contains no `prompt` handler; `windowcmd` rejects unknown operation/window names. The `windowctl` `PROMPT` setter branch returns without implementing a replacement.
- [source/input.c](../../source/input.c): `set_input_prompt` updates the normal prompt and redraws input; the redraw path expands it. The old `get_window_prompt` process-input branch is disabled with `#if 0`.
- [source/functions.c](../../source/functions.c): `alias_target` implements `$T` using `get_window_target(0)`.
