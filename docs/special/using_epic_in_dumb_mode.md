# Using EPIC in dumb mode

## What is dumb mode?

Dumb mode is a non-full-screen interface selected with the `-d` command-line option. EPIC accepts complete lines from standard input and writes normal output to standard output, making it suitable for interfaces such as screen readers. Diagnostics can also use standard error. End-of-file on standard input ends the client.

IRC commands and scripting remain available, but the original claim that everything works exactly as in full-screen mode was too broad. Screen redraws, cursor-based input editing, key-driven completion, and interactive prompt paths do not provide the same interface in this mode.

## Start the client

At a shell prompt:

```sh
epic6 -d yournick irc.server.com
```

Replace `yournick` and `irc.server.com` with the nickname and server you intend to use. The original example used the older executable name `epic5`; use the binary name installed on your system. Enter IRC commands as complete lines, for example `/join #mychan`.

## Connect with TLS

The server argument is a server description with colon-separated fields. For example:

```sh
epic6 -d yournick irc.server.com:port=6697:type=IRC-SSL
```

This requests TLS for that host and port. Port `6697` is common, but use the endpoint documented by your network. The `type=IRC-SSL` field selects the TLS connection type; choosing a port alone does not. Certificate handling follows the policy described in [SSL connections](about_ssl_connections.md).

## Configure startup commands

Place startup instructions in `~/.epicrc`. To retain the bundled default setup as a starting point, put this before your customizations:

```irc
load global
```

This is a useful setup choice, not a mandatory first line for every configuration. Current startup loads a readable personal startup file instead of `global`, so explicitly loading `global` includes its helpers and default scripts. The `-q` option disables startup loading, and `-l` or `-L` can choose a different file. See [load](../commands/load.md).

## Message NickServ after registration

EPIC's scripting language is event-driven. The [CONNECT hook](../on/connect.md) runs when the server accepts IRC registration, including after successful reconnection; it does not run for every attempted connection or guarantee that service authentication is complete.

For example, in your startup file:

```irc
on ^connect "irc.server.com 6697 *" {msg nickserv auth user@host mypass}
```

CONNECT supplies the configured hostname, configured port, and the server name reported in its welcome message. This pattern matches the first two fields and accepts any reported name. Replace the hostname, port, credentials, and NickServ command with your network's actual requirements; `auth user@host mypass` is an example, not universal NickServ syntax. Restrict the hook to the intended network so it does not send those credentials to a bot on an unrelated connection.

## Join a channel after registration

Multiple hooks can handle the same event using different serial numbers:

```irc
on #^connect + "irc.server.com 6697 *" {join #mychan}
```

The `#` introduces a serial number. Here `+` asks EPIC to choose an unused positive serial for CONNECT. Without `#`, a hook normally uses serial zero, as in the NickServ example. Hook serials run in ascending order, so negative serials precede zero and positive serials follow it. This lets the two examples coexist, but their order does not wait for NickServ's reply; a channel requiring completed authentication may need a network-specific later trigger. See [on](../commands/on.md).

## React to a private message or nickname mention

The [GENERAL_PRIVMSG hook](../on/general_privmsg.md) sees ordinary text remaining after incoming CTCP processing. It supplies:

| Argument | Meaning |
| --- | --- |
| `$0` | Sender's nickname. |
| `$1` | Original message destination. |
| `$2-` | Message text. |

Your current nickname is `$N`. This corrected version of the original example runs an external notification command for a direct message to your nickname or a message containing it:

```irc
on #^general_privmsg - * {if ([$1] == [$N] || [$2-] =~ [*$N*]) {exec beep_my_terminal}}
```

The `-` asks for an unused negative serial. The first clause compares the destination, `$1`, with your nickname; the original explanation incorrectly called the destination `$0` and its condition had an unmatched bracket. The second clause uses a wildcard substring match, not a whole-word nickname detector, so it can match longer text or interpret wildcard characters in a nickname. CTCP-only messages, including actions consumed during CTCP handling, need their own hooks.

[Exec](../commands/exec.md) launches a shell command. `beep_my_terminal` is a placeholder for an external command you supply; EPIC does not bundle it. Replace it with a suitable notification command for your environment. This negative-serial hook does not suppress normal message display merely because it uses `^`; default-display suppression by that noise level applies at serial zero.

## Source validation

- [source/irc.c](../../source/irc.c) and [Makefile.in](../../Makefile.in): `-d`, startup-file selection/options, and current executable naming.
- [source/screen.c](../../source/screen.c), [source/input.c](../../source/input.c), and [source/term.c](../../source/term.c): complete-line input, output, EOF handling, and full-screen-only input/terminal paths.
- [source/server.c](../../source/server.c) and [source/parse.c](../../source/parse.c): CONNECT timing/arguments and GENERAL_PRIVMSG dispatch/arguments after CTCP processing.
- [source/hook.c](../../source/hook.c): hook registrations, free positive/negative serial selection, order, and serial-zero suppression.
- [script/global](../../script/global) and [source/commands.c](../../source/commands.c): default script setup, LOAD, and EXEC command support.
