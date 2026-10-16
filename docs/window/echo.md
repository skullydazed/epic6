# window echo

## Synopsis

```text
/window [<window>] echo [<text>]
/window [<window>] echo "<text>" [<further-window-operations>]
```

## Description

Print text locally in the selected window, much like [echo](../commands/echo.md) with an explicit destination. With no selector, use the current window. This is useful within a longer [window](../commands/window.md) command that configures or selects windows and then prints a message.

If the text begins with a double quote, the operation reads one quoted argument and leaves following words available as further window operations. Without an opening quote, it consumes the entire remainder of the window command as text. The outer quotes are not printed.

This built-in operation does not parse echo options. Words such as `-w` are part of its text. It adds no banner or other prefix, does not change input focus or input text, and sends no IRC request. Text is subject to normal display formatting, output hooks and rewriting, logging, and lastlog/scrollback handling. Output to an invisible window stays there; hold or scrollback state can defer its visible display.

The operation uses the selected window's server and explicit output destination while preserving the surrounding output level and sender/target context. It therefore does not independently assign a new message level.

## Arguments and examples

`/window -echo` is a no-op. There are no required options or a separate return value.

```text
/window 3 echo Hello in window 3
```

Print the entire remainder, `Hello in window 3`, in window 3.

```text
/window 3 echo "Before balancing" balance
```

Print `Before balancing` in window 3, then run [window balance](balance.md) on its screen. Without quotes, the word `balance` would be printed as part of the message instead.

Use [xecho](../commands/xecho.md) for output that needs explicit message-level, logging, or other echo controls.

## Source references

- [source/window.c](../../source/window.c): `windowcmd_echo` parses quoted versus remainder text and sets the explicit output context.
- [source/output.c](../../source/output.c): `put_echo` enters the logging and display path.
- [source/screen.c](../../source/screen.c): explicit-window routing and `add_to_window` apply hooks, rewriting, logs, lastlog, scrollback, and visibility rules.
