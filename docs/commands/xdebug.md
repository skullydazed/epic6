# xdebug command

## Synopsis

```text
/xdebug [+|-]option ... [{ commands }]
/xdebug
```

## Description

`xdebug` controls development diagnostics and selected compatibility or experimental behavior. These flags are implementation-specific and can change between versions. Some affect parsing or runtime behavior rather than merely adding messages; use the flags needed for a particular investigation rather than enabling everything.

With no arguments, the command lists accepted option names in a usage message. It does not display their current states. The [`xdebug` function](../functions/xdebug.md) returns enabled/disabled state information.

A bare option or `+option` enables its bit; `-option` disables it. These are not toggles. Processing starts in enable mode, and an explicit sign sets the mode for subsequent unsigned options as well: `-inbound outbound` disables both, while `-inbound +outbound` disables inbound and enables outbound. Options are case-insensitive and matched by first prefix in table order, so ambiguous abbreviations are not rejected. Unknown options report `Unrecognized XDEBUG option` but do not cancel other changes.

An optional trailing brace block runs with the changed flags, in the current scope and enclosing argument context. When the block returns, the entire original flag mask is restored, including changes made by nested `xdebug` commands. Without a block, changes persist. The flags initially start at zero, although startup code or scripts can change them.

## Options

| Flags | Area affected |
| --- | --- |
| `inbound`, `outbound` | Trace server input/output and related queue activity. |
| `local_vars`, `unknown` | Variable lookup/assignment and unknown-function diagnostics. |
| `newio`, `waits`, `sequence_points` | Event processing and status update diagnostics. |
| `ctcps`, `scram`, `recode` | CTCP, encryption, and character recoding diagnostics. |
| `server_connect`, `kwarg_parse`, `rfc1459`, `ssl` | Connection, server-description, protocol, and TLS diagnostics. |
| `channels`, `lastlog`, `message_from` | Channel state, history, and output-context diagnostics. |
| `regex_debug`, `new_math_debug`, `extractw_debug`, `unicode` | Expression/word/regex/Unicode development diagnostics. |
| `dword` | Enable double-quoted grouping for word operations that explicitly use dword mode. It does not change every parser. |
| `extractw` | Enable extended word behavior for operations using extractw mode. |
| `slash_hack` | Change compatibility behavior in the legacy expression parser. |
| `broken_clock` | Select the alternative clock-reset path. |
| `crash` | Change behavior in fatal-error handling, including waiting before termination. |
| `notify`, `who_queue` | Registered bits with no current C consumer. |
| `memory`, `no_color`, `regex`, `bold_helper`, `new_math`, `autokey` | Accepted compatibility names with zero bits; enabling/disabling them changes nothing. |
| `all` | Enable every bit; `-all` clears every bit, including behavior flags. |

Diagnostics are emitted only when their corresponding code paths run. TLS diagnostics require the related TLS path to be used; a flag does not itself establish a connection or activate a subsystem. Debug text uses EPIC's diagnostic output mechanism rather than automatically creating a debug log file. [`debuglog`](debuglog.md) is a separate facility.

## Examples

Trace server traffic:

```text
/xdebug +inbound +outbound
/xdebug -inbound -outbound
```

Temporarily enable double-quoted word handling in a script:

```text
xdebug +dword {
    assign items one
    push items two three
    echo $items
}
```

The block displays `one "two three"`; the previous flag mask is restored afterward. Variable changes made by the block remain.

## Restrictions and source

The command is always registered and requires no IRC connection to change flags. It has no function return value or normal success confirmation. Diagnostics can include full server traffic and script values, so consider the actual text before sharing a trace.

[source/debug.c](../../source/debug.c) defines the option table and `xdebugcmd()`. [include/debug.h](../../include/debug.h) defines flag bits. [source/output.c](../../source/output.c) implements diagnostic output, and the flag consumers are in the corresponding network, parser, event, and history source files. [source/commands.c](../../source/commands.c) registers the command.
