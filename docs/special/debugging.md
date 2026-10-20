# Debugging scripts

Several client facilities help you inspect script execution, reproduce an event, and isolate a faulty definition. They provide different views: script tracing, protocol traffic, synthetic events, local output, and definition management are not interchangeable.

## Execution tracing

[Set debug](../set/debug.md) controls script-execution tracing with a nonnegative integer mask:

| Value | Trace class |
| --- | --- |
| `1` | Statements being executed. |
| `2` | Expando and selected expression substitutions. |
| `4` | Function calls, expanded arguments, and returned values. |

Add values to combine them: `3` traces statements and substitutions, and `7` enables all three classes. The default is `0`. Tracing affects subsequent execution immediately and can generate substantial local output. It is not a comprehensive record of every variable assignment or every internal C call.

```text
/set debug 7
/echo Result: $toupper(example)
/set debug 0
```

To preserve a previous setting while inspecting a script fragment, save and restore it through [stack](../commands/stack.md), rather than assuming it was initially zero:

```text
stack push set debug;
set debug 3;
echo Tracing this fragment;
stack pop set debug;
```

Trace output can include expanded message text, arguments, and credentials used by the script. Ordinary diagnostic tracing is privileged output and is not necessarily suppressed by the same hooks that filter normal messages. Select the classes needed for the problem and restore the prior state afterward.

## Inspecting values and calls

Well-placed [echo](../commands/echo.md) or [xecho](../commands/xecho.md) statements show whether a branch ran and what values it used. Delimit values, for example `echo value=[$value]`, to make empty values and surrounding spaces visible. Remember that ordinary output hooks can change or suppress these messages.

[Eval](../commands/eval.md) expands and executes script text. It is useful for trying a small expression or command fragment at the input prompt, but it is also a normal programming facility; it is not limited to debugging. An evaluated fragment can send messages or change state just like other executed code.

[Call](../commands/call.md) prints the current script-scope call stack. Put it inside the alias or callback under investigation to see the active chain. It is not a native C backtrace or a dump of all local variables.

## Protocol and internal diagnostics

[Xdebug](../commands/xdebug.md) manages named internal categories separately from `set debug`. For protocol traffic, enable the specific directions and disable them afterward:

```text
/xdebug +inbound +outbound
/xdebug -inbound -outbound
```

These categories report incoming/outgoing server traffic from instrumented code paths and can expose authentication and message data. If categories were already enabled, preserve their original state instead of unconditionally disabling them. Some `xdebug` categories affect parsing or compatibility behavior, so enabling everything is not simply a more verbose version of the same client.

[Debuglog](../commands/debuglog.md) controls a separate diagnostic file. `debuglog on` appends to `debug.log` in the current working directory; `debuglog off` closes it. Its contents come from internal `debuglog()` calls and are not a complete command or IRC transcript. File opening can fail, and the command does not verify all later writes.

## Reproducing events

| Tool | What it does | Important distinction |
| --- | --- | --- |
| [pretend](../commands/pretend.md) | Parses a supplied line as though received from the current server. | Requires an open server connection and can update real client state or invoke handlers that send requests. |
| [shook](../commands/shook.md) | Invokes a named hook with supplied argument text. | Does not reconstruct protocol state, sender userhost, or all event metadata. |
| [hook](../commands/hook.md) | Generates the script-defined hook event. | Useful for a deliberately designed script interface, not a replacement for parsing an arbitrary server message. |

Synthetic hooks can run production handlers. Use the individual event's documented argument layout and do not infer that a handler's success proves the corresponding network event would behave identically.

## Reloading and resetting definitions

[Package](../commands/package.md) and [unload](../commands/unload.md) let a script group and remove its definitions before reloading, often avoiding a client restart. Unloading does not reverse arbitrary earlier effects: already sent IRC commands, external processes, file writes, changed settings, or unrelated definitions can remain.

[Dump](../commands/dump.md) removes selected classes of aliases, global variables, or hooks. It is not a universal reset to a known initial session; it can also remove support definitions that other scripts need. Use targeted removal or a separate client session when that is a better fit for the investigation.

[Abort](../commands/abort.md) deliberately terminates the process through the C library's abort operation. It is for native crash investigation, not routine script tracing. Core-file creation and location depend on the operating system and resource limits; normal exit cleanup is not performed.

## Contributed debugging script

The supplied [debug.irc](../scripts/debug.irc.md) script demonstrates selective tracing and timing. It provides `debug_section`/`debug_endsection`, `call.trace`, `call.xtrace`, and `call.time`. These helpers manipulate logging, settings, and hooks, and have their own restoration limits; read that page before using them in a session with existing custom diagnostics.

The built-in tracing and inspection tools do not need an external debugger or optional platform feature. A native debugger or operating-system core-dump facility is separate. Each command has its own output and effects; there is no common debugging return value or automatic test-success result.

## Source references

- [include/vars.h](../../include/vars.h), [source/commands.c](../../source/commands.c), [source/expr.c](../../source/expr.c), and [source/functions.c](../../source/functions.c): debug-mask classes and tracing sites.
- [source/debug.c](../../source/debug.c) and [source/server.c](../../source/server.c): `xdebug` categories and protocol trace calls.
- [source/debuglog.c](../../source/debuglog.c): diagnostic-file logging.
- [source/hook.c](../../source/hook.c): synthetic event dispatch.
- [source/alias.c](../../source/alias.c): call-stack reporting and definition removal.
- [script/debug.irc](../../script/debug.irc): contributed tracing and timing helpers.
