# sendline

Process supplied text using the interactive input-line parser.

## Synopsis

```text
/sendline [text]
```

## Description

`sendline` treats its argument text as a line submitted for interactive processing. Without an initial slash, the text is sent to the current window's query, or its current channel when there is no query. With an initial slash, it is interpreted as an EPIC command. The choice is made from the beginning of the supplied text; a leading space is not automatically removed to discover a later slash.

A single slash permits normal alias or built-in command lookup. `//command` bypasses an alias and selects the built-in command if available. `/^command` requests quiet ordinary command display. Unknown commands follow the normal `unknown_command` hook and `dispatch_unknown_commands` setting. See [on](on.md).

The handler does not add a dollar-expansion pass to ordinary command text. The surrounding script may already have expanded its arguments, and an invoked alias expands its own body normally. Expressions and explicit command blocks still use their ordinary parser rules. Unlike [eval](eval.md), `sendline` does not generally treat a plain supplied line as a semicolon-separated script block.

There are no options or default text. Empty text causes no statement action. The command is always registered and returns no value; output and effects depend on the line it processes. Text sent to IRC uses the same destinations, hooks, encoding, limits, and connection requirements as [send](send.md).

## Input hooks and client context

Pressing Enter normally invokes the `send_line` binding, which raises the `input` event before submitting the line to this parser. If an input hook suppresses that default submission, it can call `sendline` to submit an unchanged or rewritten line itself. A hook that permits the default should not submit it again unless duplicate processing is intended.

`sendline` does not raise `input` again, so it can be used from that hook without recreating the same input event. It also does not simulate the whole `send_line` binding: it does not clear the input buffer, resolve a modal input response, or perform the binding's hold/scrollback adjustments. It redraws the input display after parsing.

The handler temporarily enables ordinary command display and restores the previous display setting afterward. It also restores the original server context after processing, even if the submitted command changes that context. It does not itself select the current window's server as the Enter binding does; it starts with the server context in which it was called.

## Examples

Send literal text to the current conversation:

```text
/sendline hello there
```

Execute an EPIC command:

```text
/sendline /echo hello there
```

Replace default input processing with an explicit submission:

```text
/on ^input "*" {sendline $*}
```

This suppressing hook submits the line once through `sendline`, rather than letting the Enter binding submit it afterward.

## History and source

`sendline` first appeared in ircII.

Registration, `sendlinecmd`, and interactive command parsing are in [source/commands.c](../../source/commands.c). The Enter binding and `input` event are in [source/input.c](../../source/input.c); current query/channel selection is in [source/window.c](../../source/window.c).
