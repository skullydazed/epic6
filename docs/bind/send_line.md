# send_line

## Synopsis

```text
/bind <key> send_line
```

## Description

Submit the contents of the input line for processing. This is the ordinary operation of the Enter key. The built-in action copies the line, clears the input buffer, resets the cursor and horizontal input position, and redraws input before processing the copied text.

Normally, text without a leading `/` is sent to the current window's target, while a command beginning with `/` is executed in the current window's context. The action first offers the line to [on input](../on/input.md). If that hook suppresses normal processing, the line is not parsed by the built-in handler. Otherwise it uses the same interactive statement parser as [sendline](../commands/sendline.md).

If a line-input prompt is active, such as one created by [input](../commands/input.md), the line goes to that prompt's callback instead. That path restores the saved input line and bypasses the ordinary `on input` hook and statement parser.

The action takes no arguments or options; extra text supplied with the binding is ignored. Its output and errors depend on the submitted text, hooks, or prompt callback. Text sent to an IRC target requires a suitable connection and target; delivery and rejection depend on the server. An empty line still goes through the hook or prompt callback, but the ordinary statement parser performs no command for it.

The default bindings are carriage return (`^M`, normally Enter) and line feed (`^J`, Control-J). Scripts can change them.

## Hold and scrollback effects

With hold mode enabled, the action advances the held view by the window's hold-slider amount. It does this before processing when the window was not already holding excess output, or afterward when it was. This advances the view without disabling hold mode.

If hold mode remains enabled after processing and the window was initially scrolled back by more than its output height, the action also performs [scroll_forward](scroll_forward.md). Pressing Enter can therefore help catch up with buffered output. These effects also apply when the line is empty or handled by a prompt or hook.

## Example

```text
/bind ^M send_line
```

With `/echo hello` in the input line, pressing Enter clears that input and runs the command, printing `hello` locally. With ordinary message text instead, the normal parser sends it to the current target unless a hook intercepts it.

See [bind](../commands/bind.md) for key sequence syntax.

## Source references

- [source/keys.c](../../source/keys.c): action registration and default bindings.
- [source/input.c](../../source/input.c): `send_line` implements buffer clearing, server context, prompt/hook dispatch, and hold/scrollback effects.
- [source/commands.c](../../source/commands.c): `parse_statement` implements interactive text/command dispatch; `sendlinecmd` uses the same parser.
- [source/screen.c](../../source/screen.c): `fire_normal_prompt` restores saved input and invokes a prompt callback.
- [source/window.c](../../source/window.c): `window_is_holding`, `window_is_scrolled_back`, and `unhold_a_window` determine and advance held output.
