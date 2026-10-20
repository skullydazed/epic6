# slowcat script

## Synopsis

```text
/load slowcat
/slowcat <filename>
```

## Description

The `slowcat` alias reads a local text file and sends it to the window's current target through the built-in [send](../commands/send.md) command. It schedules one line every half-second, nominally two lines per second, to reduce the chance of triggering server flood controls. This rate is fixed and is not a guarantee that a server will accept the traffic.

There is one required filename and no script-specific options or settings. Extra arguments are ignored. Use a simple filename or path without spaces: the filename value is interpolated into `open` without protective quoting. Loading the script defines the alias but sends nothing by itself.

## Operation and target

The file is opened immediately for reading. Failure prints `<filename> does not exist or I can't open it.` and returns. On success, the named repeating timer `slowcat` starts; the first read occurs after its initial half-second delay. Each tick reads and sends one line, or closes the file and deletes the timer after EOF has been detected.

When invoked interactively at the input prompt, the timer is associated with the initiating window, so changing to another window does not normally redirect it. It still resolves that window's target on every tick, with query taking precedence over channel. Changing that query, channel, or server association while a send is running can redirect subsequent lines. If the associated window disappears, ordinary timer fallback rules apply rather than a saved immutable target.

Invocation from a server-message hook can instead create a server-associated timer, which uses that server's current window when it runs. Avoid starting it from hooks, especially ones that can fire repeatedly.

The script sends file text as messages, not as client commands; a line beginning with `/` is not passed through interactive command parsing. Normal outgoing hooks, encoding, line limits, connection requirements, and server policy still apply. The script does not confirm delivery or retry a failed message.

## Limitations and cleanup

Only one slowcat is supported at a time. Every call overwrites the shared `slowcat.fd`; the timer name is also fixed. Starting another call while the timer exists can fail to create the new timer after opening the new file, leaving the old timer to read the replacement descriptor and losing the previous open descriptor. Do not use repeated calls as a queue.

Input line endings are removed by the file reader; text with invalid UTF-8 is subject to the client's script-input recoding. This is a text-message sender, not binary transfer or exact file reproduction. Empty lines do not carry useful message text. For a newline-terminated file, EOF is generally noticed only after a read past the last line, so there can be a final empty send attempt before the next tick performs cleanup. No separate read-error handling is implemented.

Timer callbacks run in the client event loop. A busy or paused client can delay execution, and overdue repeated timers can run close together afterward. The nominal rate is not a strict network pacing guarantee.

There is no dedicated cancel command. Deleting the timer with `/timer -delete slowcat` stops future reads but does not close the file descriptor; manual cleanup through the file functions is then required. The script provides no progress indicator, completion announcement, function-return status, or automatic cancellation on target loss. It needs a readable local file but no external program, helper script, or optional platform feature.

## Example

```text
/load slowcat
/slowcat ~/announcement.txt
```

Select the intended channel or query before starting. Wait for that file to finish before starting another one, and keep the initiating window's target unchanged.

## History

Slowcat first appeared in EPIC5-0.0.3. The source records hop's implementation for larne on March 26, 2004, with the name suggested by AnguzHawk.

## Source references

- [script/slowcat](../../script/slowcat): shared descriptor, half-second timer, EOF cleanup, and built-in sends.
- [source/files.c](../../source/files.c): `file_read` removes newlines and recodes invalid text; `file_eof` reports stream state.
- [source/functions.c](../../source/functions.c): `open`, `read`, `eof`, and `close` wrappers.
- [source/timer.c](../../source/timer.c): default window/server association, duplicate timer rejection, repeat scheduling, and fallback context.
- [source/commands.c](../../source/commands.c): `send_to_query_first` sends the supplied text without interactive command interpretation.
- [source/window.c](../../source/window.c): `get_window_target` selects query before channel.
