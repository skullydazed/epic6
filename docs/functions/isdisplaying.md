# $isdisplaying()

## Synopsis

`$isdisplaying()`

## Technical

Returns EPIC's current ordinary-output display flag. It accepts no arguments; supplied text is ignored. Normally the flag is `1` when ordinary output is enabled and `0` when it is suppressed.

The flag is temporarily disabled during [load](../commands/load.md), for commands or aliases prefixed with the caret modifier (for example, `/^msg` at the input line), and while hooks whose noise level disables display run. This includes quiet, silent, and normal [on](../commands/on.md) handlers; noisy handlers enable display. The surrounding flag is restored afterward. Commands executed inside these handlers inherit their display flag unless another command changes it.

[echo](../commands/echo.md) temporarily enables the flag while outputting its text. [sendline](../commands/sendline.md) enables it while processing its input, and [xeval](../commands/xeval.md) with `-noisy` enables it for its commands. [xecho](../commands/xecho.md) normally forces display, while `xecho -say` declines output when this flag is disabled. `echo` itself does not parse a `-say` option.

For ordinary messages, a disabled flag also prevents the outgoing `send_msg`, `send_public`, and `send_notice` display hooks from running. It does not prevent the IRC message from being sent.

This flag is separate from output-context suppression set by [shh](../commands/shh.md). A result of `1` does not guarantee that particular output reaches the screen: context suppression, hooks, routing, and window visibility can still affect it. A result of `0` also does not prevent commands that explicitly force output from producing it.

## Practical

Use this to decide whether optional chatter should be produced, or whether to handle it another way when ordinary display is disabled. It indicates whether `xecho -say` passes its display-flag check, rather than guaranteeing that the text will be visible.

## Returns

The current integer display flag, normally `0` or `1`. The function reads the flag directly and does not change it.

## Examples

```text
if (isdisplaying()) {xecho -say Optional progress message}
```

The function is evaluated before `xecho` changes any output state.

## Source

Registered and implemented as `function_isdisplaying` in [source/functions.c](../../source/functions.c). The display flag is in [source/output.c](../../source/output.c), command overrides and outgoing hooks are in [source/commands.c](../../source/commands.c), and hook noise handling is in [source/hook.c](../../source/hook.c).
