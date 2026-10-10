# shh

## Synopsis

```text
/shh
```

## Technical

`shh` suppresses ordinary display output associated with the **current output context**. It sets that context's suppression flag and produces no output or script return value. It accepts no options; supplied arguments are ignored. In particular, `shh {commands}` does not execute a command block.

An output context identifies the server, window, sender, target, and output level for an operation. EPIC offers newly created contexts to [on](on.md) `context` handlers, which can call `shh` to suppress their output. The bundled [ignore script](../../script/ignore) uses this mechanism.

The flag stays set for that context's lifetime. A nested output context begins unsuppressed, and popping it restores the enclosing context, including its existing suppression state. Returning from a script handler does not itself undo `shh`; suppression ends when EPIC releases that output context. There is no argument to toggle the flag back off.

## Practical

Use `shh` inside an `on context` handler when the context's sender, target, or level identifies output you want to hide. It changes display handling, rather than canceling the underlying event or stopping subsequent script commands. IRC state changes, hooks, and network activity can still occur.

Ordinary suppressed output does not reach the screen, window scrollback, lastlog, window logfile, or separate `log` destinations handled by window output. It also skips the usual `on window` display hook. However, EPIC's global logfile receives output before this suppression check, so `shh` does not remove it from every log.

## Restrictions

Suppression applies to output routed through that context. Commands that create a new context, such as [xecho](xecho.md), can produce visible output afterward if context handlers do not suppress that new context. Ordinary [echo](echo.md) inherits the current context and remains suppressed. Direct terminal output and output before a normal window exists are outside this mechanism. This is not a general wrapper for making a command or block quiet, and it does not change the global command-display setting.

## Examples

```text
on #^context 10 "% % % #quiet %" {shh}
```

This installs a serial-numbered context handler matching a target of `#quiet`, regardless of the other four context fields. It suppresses ordinary output produced in those contexts; it does not part the channel, prevent processing its messages, or guarantee that separately generated output will be hidden. The same handler mechanism is used by script-provided ignore rules.

## Source

Registration is in [source/commands.c](../../source/commands.c). `shhcmd()`, context creation, and context removal are in [source/window.c](../../source/window.c). Suppression during screen and window output is in [source/screen.c](../../source/screen.c). Global logging before screen routing is in [source/output.c](../../source/output.c).
