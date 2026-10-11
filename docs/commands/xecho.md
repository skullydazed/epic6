# xecho command

## Synopsis

```text
/xecho [options ...] text
```

## Description

Like [`echo`](echo.md), `xecho` displays local text, with additional control over routing and presentation. It normally forces ordinary display on while emitting text and creates a fresh output context. Consequently, it can display despite a suppressed enclosing context; a hook can still suppress the new context or its window output. It sends no IRC message and returns no function value.

Without routing options, it inherits the enclosing sender, target, level, and explicit window selection. Options are processed while the next text begins with a hyphen. Most are recognized by their first letter, not by validating the full spelling. `-line` is distinguished from `-level` by its second letter, and `-as` from `-all` by its second letter. Use the documented spellings to avoid accidentally consuming message text as an option or argument.

## Options

| Option | Effect |
| --- | --- |
| `-current` or `-c` | Select the current window. |
| `-visible` or `-v` | Select the current window if visible, otherwise the last input screen's input window or another visible window. |
| `-window reference` or `-w reference` | Select a window by reference/name, or by a channel associated with it on the current server. `-1` selects the current window for compatibility; another invalid selector silently stops the command. |
| `-level level` or `-l level` | Set one output level and clear the inherited target. This accepts a single level name/prefix, not a comma-separated mask; an unrecognized level leaves the prior level/target unchanged. |
| `-target target` or `-t target` | Set the output target for normal routing. Put this after `-level`, which clears it. |
| `-line number` | Set the selected window's replacement line, zero-based and below its display height. Requires a selected window, normally supplied with `-window` first. |
| `-all` or `-a` | Emit once to every window, visible or hidden. |
| `-as` | Emit once to every window associated with the current server. |
| `-banner` or `-b` | Prefix text with the current banner and one space. |
| `-raw` or `-r` | Immediately write all remaining bytes to the selected visible screen's terminal, or the current screen. This ends option processing and bypasses normal window output. |
| `-nolog` or `-n` | Inhibit file logging while normal text is emitted. This does not prevent lastlog or scrollback retention. |
| `-say` or `-s` | Return without output if ordinary display is currently suppressed. This tests the display flag, not the enclosing context's suppression flag. |
| `-x` | Temporarily use normalization instead of the current display mangling rules. |
| `-f` | Suppress hidden-window notifications for this output. |
| `-e seconds` | Set expiry for the emitted history entry; accepted input is an integer string. Use positive whole seconds; decimal fractions are ignored. Zero disables expiry. |
| `--` | End option processing; all remaining text is the message. |

An unrecognized leading option is displayed as text instead of producing an error. Missing option values have option-specific behavior, often leaving prior routing unchanged. With no text, normal output can emit an empty line. Quotes around text are not removed by this handler.

`-all`/`-as` use explicit per-window destinations and override single-window routing. If both are supplied, `-all` wins. An explicit window also takes priority over level/target routing. Level selection affects history/log classification as well as window selection.

## Output and restrictions

Normal output can pass through the window hook, output rewriting, file logs, lastlog, scrollback, and notification machinery. `-nolog` prevents file writes but does not bypass those hooks or rewriting. Global logging occurs before the window hook, so suppressing a window's display alone does not necessarily suppress that global log entry. Held or hidden windows can retain output without showing it immediately.

Expiry is implemented through lastlog timestamps and a timer that removes expired entries and rebuilds affected scrollback. It does not erase copies already written to a file, and actual removal can be delayed while the client is busy. `-line` replaces display content rather than changing the original file-log record.

After normal emission, the handler restores display, logging inhibition, mangling, notification, expiry, and output context. However, early returns such as `-raw`, a suppressing `-say`, invalid window selection, or invalid line selection bypass that final restoration. If placed after `-x`, `-f`, or `-e`, they can leave those temporary global settings changed. Put validation/routing and `-say` before such modifiers, and use `-raw` by itself when sending terminal control bytes.

The command is always registered and needs no IRC connection. Raw terminal output is platform/terminal-dependent, bypasses normal logs/history/hooks, and can alter terminal state. Normal display follows fullscreen and window availability behavior.

## Examples

```text
xecho -v This text appears in a visible window
xecho -l snotice -w misc This appears in the misc window
xecho -a This appears in all windows
xecho -b This includes the banner
xecho -e 5 This message expires after approximately five seconds
xecho -- -= This text begins with a hyphen =-
```

Replace `misc` with an existing window name. For a terminal supporting the xterm title sequence:

```text
eval xecho -r $chr(27)]2\;Your titlebar has been changed.$chr(7)
```

## Source

[source/commands.c](../../source/commands.c) registers `xecho` and implements `xechocmd()`, options, and restoration. [source/window.c](../../source/window.c) manages output contexts and selected lines; [source/screen.c](../../source/screen.c) routes, rewrites, and displays window output. [source/output.c](../../source/output.c) implements `put_echo()`, [source/log.c](../../source/log.c) checks logging inhibition, and [source/lastlog.c](../../source/lastlog.c) manages expiry. [source/levels.c](../../source/levels.c) parses single levels.
