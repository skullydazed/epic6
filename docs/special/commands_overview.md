# Overview of commands

Commands let you interact with IRC servers, change the client interface, and program EPIC. The groups below provide a navigation guide to the current built-in command set. Follow each link for syntax, arguments, defaults, errors, and examples; commands in one group do not necessarily share those details.

At the input prompt, begin a command with `/`, for example `/join #example`. Ordinary text is normally sent to the current target. Inside a script [block](block.md), statements are interpreted as commands or expressions according to [ircII syntax](ircii_syntax.md). Command names are case-insensitive; argument text can still be case-sensitive or interpreted by the server.

An alias can replace the normal behavior of a built-in command with the same name. A double prefix such as `//echo text` bypasses the alias and selects the built-in command when present. If a name is unrecognized, configured unknown-command dispatch or an `unknown_command` hook can affect its handling; it is not necessarily a locally rejected typo.

## IRC commands

| Task | Commands | Purpose |
| --- | --- | --- |
| Channels | [invite](../commands/invite.md), [join](../commands/join.md), [kick](../commands/kick.md), [knock](../commands/knock.md), [mode](../commands/mode.md), [names](../commands/names.md), [part](../commands/part.md), [topic](../commands/topic.md), [who](../commands/who.md) | Invite or join, manage channel state, request membership information, or leave. Permissions and returned data depend on the server. |
| Messages and actions | [msg](../commands/msg.md), [notice](../commands/notice.md), [me](../commands/me.md), [describe](../commands/describe.md), [say](../commands/say.md), [send](../commands/send.md), [wallchops](../commands/wallchops.md), [wallops](../commands/wallops.md) | Send messages, notices, or actions. `send` prefers the current query over channel; `say` prefers the current channel. |
| Client information requests | [ctcp](../commands/ctcp.md), [ping](../commands/ping.md) | Send CTCP requests; `ping` measures a client CTCP response, not an operating-system ICMP ping. |
| Network information | [admin](../commands/admin.md), [info](../commands/info.md), [links](../commands/links.md), [list](../commands/list.md), [lusers](../commands/lusers.md), [map](../commands/map.md), [motd](../commands/motd.md), [stats](../commands/stats.md), [time](../commands/time.md), [trace](../commands/trace.md), [version](../commands/version.md) | Request server/network information. Some commands have local display behavior as well; see each command page. |
| Identity and presence | [away](../commands/away.md), [nick](../commands/nick.md), [ison](../commands/ison.md), [userhost](../commands/userhost.md), [whois](../commands/whois.md), [whowas](../commands/whowas.md) | Set presence or nickname and request user information. Queries can have asynchronous script callbacks. |
| Operator requests | [connect](../commands/connect.md), [deop](../commands/deop.md), [die](../commands/die.md), [kill](../commands/kill.md), [oper](../commands/oper.md), [rehash](../commands/rehash.md), [restart](../commands/restart.md), [squit](../commands/squit.md) | Request operator actions or authentication. `connect` asks a server to link servers; it does not establish your own client connection. |
| Network-specific requests | [rping](../commands/rping.md), [servlist](../commands/servlist.md), [silence](../commands/silence.md), [squery](../commands/squery.md), [uping](../commands/uping.md), [userip](../commands/userip.md), [usrip](../commands/usrip.md), [note](../commands/note.md) | Send requests whose availability, syntax, and meaning depend on the network. Recognition by EPIC is not server support. |
| Protocol text | [quote](../commands/quote.md), [xquote](../commands/xquote.md) | Forward protocol text, with `xquote` providing additional server/encoding controls. |

## Programming

| Task | Commands | Purpose |
| --- | --- | --- |
| Control flow | [break](../commands/break.md), [continue](../commands/continue.md), [do](../commands/do.md), [fe](../commands/fe.md), [fec](../commands/fec.md), [for](../commands/for.md), [foreach](../commands/foreach.md), [if](../commands/if.md), [repeat](../commands/repeat.md), [return](../commands/return.md), [switch](../commands/switch.md), [unless](../commands/unless.md), [until](../commands/until.md), [while](../commands/while.md) | Conditionals, loops, iteration, and control transfer. `return` has function-result behavior when used in a function context. |
| Definitions and events | [alias](../commands/alias.md), [on](../commands/on.md), [hook](../commands/hook.md), [shook](../commands/shook.md), [stub](../commands/stub.md) | Define commands/functions and event handlers, generate script events, or arrange definitions loaded on demand. |
| Variables and storage | [assign](../commands/assign.md), [local](../commands/local.md), [push](../commands/push.md), [pop](../commands/pop.md), [shift](../commands/shift.md), [unshift](../commands/unshift.md), [stack](../commands/stack.md) | Assign values, create locals, manipulate word lists, and save or restore supported definitions/state. |
| Evaluation and context | [eval](../commands/eval.md), [xeval](../commands/xeval.md), [bless](../commands/bless.md), [call](../commands/call.md) | Evaluate code, select window/server context, expose a locked local frame, or inspect the call stack. See individual scope rules. |
| Scheduling and waits | [defer](../commands/defer.md), [queue](../commands/queue.md), [timer](../commands/timer.md), [wait](../commands/wait.md), [pause](../commands/pause.md), [sleep](../commands/sleep.md), [usleep](../commands/usleep.md) | Defer/queue execution, schedule callbacks, wait for events, or pause. These commands do not all have the same blocking or callback behavior. |
| Scripts and packages | [load](../commands/load.md), [which](../commands/which.md), [package](../commands/package.md), [subpackage](../commands/subpackage.md), [unload](../commands/unload.md) | Load or locate scripts and associate definitions with packages for later removal. `unload` is package removal, not reversal of every effect a file caused. |
| Input and output | [echo](../commands/echo.md), [xecho](../commands/xecho.md), [input](../commands/input.md), [input_char](../commands/input_char.md), [sendline](../commands/sendline.md), [parsekey](../commands/parsekey.md), [type](../commands/type.md), [xtype](../commands/xtype.md) | Display text, request input, process an input line, invoke a key action, or edit/simulate input. `sendline` can execute commands, not merely send text. |
| Testing and diagnostics | [abort](../commands/abort.md), [call](../commands/call.md), [debuglog](../commands/debuglog.md), [dump](../commands/dump.md), [pretend](../commands/pretend.md), [xdebug](../commands/xdebug.md) | Diagnostics, intentional abort, definition removal, synthetic protocol input, and debugging controls. These are operational commands with effects described on their own pages. |

## Client interface and connections

| Task | Commands | Purpose |
| --- | --- | --- |
| Windows | [window](../commands/window.md), [clear](../commands/clear.md), [unclear](../commands/unclear.md), [shh](../commands/shh.md), [lastlog](../commands/lastlog.md), [log](../commands/log.md) | Manage windows and display views, inspect retained output, and control logging. `shh` suppresses output in the current output context; it is not server-side silence. |
| Configuration | [set](../commands/set.md), [bind](../commands/bind.md), [rbind](../commands/rbind.md), [encoding](../commands/encoding.md) | Query/change registered settings, install or inspect key bindings, and manage character encodings. |
| Server connections | [server](../commands/server.md), [disconnect](../commands/disconnect.md), [reconnect](../commands/reconnect.md), [hostname](../commands/hostname.md), [vhosts](../commands/vhosts.md) | Select and manage your own server connections and local host choices. Automatic retry policy can additionally come from a script. |
| Local environment | [cd](../commands/cd.md), [setenv](../commands/setenv.md), [exec](../commands/exec.md), [beep](../commands/beep.md), [license](../commands/license.md) | Change directory/environment, run local processes, signal the terminal, or display license information. |
| Exit | [quit](../commands/quit.md), [exit](../commands/exit.md) | Exit the client through the built-in quit handling. |

## Script-provided commands

The available command vocabulary also depends on loaded scripts. For example, the supplied [builtins](../scripts/builtins.md) environment defines compatibility aliases such as `bye` and `ircname` and loads the [notify](../scripts/notify.md) MONITOR wrapper. Optional scripts provide commands such as [save](../scripts/save.md), [scan](../scripts/scan.md), and [renumber](../scripts/renumber.md). These names are not proof that a corresponding C built-in exists.

A personal startup file can replace the default [global](../scripts/global.md) setup. Consequently, two installations or sessions with the same executable can expose different aliases, pseudo-settings, completion behavior, and hooks. Load the relevant script and its dependencies before relying on its commands.

## External language support

When compiled with Python support, [python](../commands/python.md) and [pydirect](../commands/pydirect.md) provide Python execution interfaces. Availability is conditional on the build and does not imply that every legacy embedded-language interface is present. Local process execution through `exec` is separate from embedded Python support.

## Server-dependent behavior

Server requests are subject to connection state, network extensions, privileges, target visibility, and server policy. A command can be recognized and transmitted while the server rejects it or returns different information. Operator commands do not grant privileges merely because they are listed here. Commands that only modify local windows or script definitions need not send any IRC request.

This overview has no single return value or default argument set. A command's displayed output, script function results, asynchronous callbacks, and effects on client/server state are separate interfaces. Consult the individual page rather than inferring behavior from a shared C handler or a familiar command name.

## Examples

```text
/window list
/set clock on
/echo Local client text
```

These inspect windows, enable a registered setting, and print locally. To request a server operation, use its documented command, for example `/motd` on a connected session. A server reply is asynchronous and is not the return value of a script function.

## Source references

- [source/commands.c](../../source/commands.c): built-in command table, alias precedence, double-prefix handling, interactive parsing, and unknown-command dispatch.
- [source/alias.c](../../source/alias.c): aliases, variables, stubs, packages, and definition removal.
- [source/if.c](../../source/if.c): conditionals and iteration.
- [source/hook.c](../../source/hook.c) and [source/keys.c](../../source/keys.c): event and key-binding interfaces.
- [source/server.c](../../source/server.c), [source/window.c](../../source/window.c), and [source/who.c](../../source/who.c): connections, local windows, and server-query callbacks.
- [source/python.c](../../source/python.c): optional Python integration.
- [script/builtins](../../script/builtins) and [script/global](../../script/global): supplied aliases and startup dependencies.
