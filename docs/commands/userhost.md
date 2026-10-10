# userhost command

## Synopsis

```text
/userhost [-direct] [-count number] [-extra variable] [nickname ...] [-cmd { commands }]
/userhost -flush
```

## Description

`userhost` asks the server for the username and hostname of each specified nickname. With no nicknames and no callback, it queries your own nickname. It splits requests into groups of five by default. Replies are processed through a per-server queue; normally only one request is outstanding at a time, with further batches sent as replies arrive. Large requests can still encounter server flood limits.

Without `-cmd`, EPIC queries the server and displays each returned nickname as `nickname is username@hostname`, with operator and away annotations when applicable. Nicknames omitted from the reply produce no ordinary display. The numeric 302 hook can handle or suppress each displayed entry. Its arguments are nickname, operator sign, availability sign, username, hostname, and extra data: the availability sign is `-` for away and `+` for available.

With `-cmd`, the remaining text is a callback body, optionally enclosed in braces. It runs for each requested nickname in request order. A nickname missing from the reply receives `<UNKNOWN>` for both username and hostname, with both callback flags set to `-`. Callback processing replaces the normal display and per-entry numeric hook.

## Asynchronous and cached behavior

A server request returns before its reply arrives; its callbacks execute later. However, when every requested nickname is present in EPIC's userhost cache, a `-cmd` request executes all callbacks synchronously before `userhost` returns. If any nickname is absent from the cache, the entire list is queried from the server. `-direct` forces the server path.

Cached callbacks report non-operator and not-away regardless of actual status. Use `-direct` when those flags matter, or when you need a fresh server response. Hostnames returned by a server may be cloaked or otherwise restricted by its privacy policy.

Callbacks receive their own argument context and local scope, rather than retaining the enclosing command's positional arguments or local variables. Use `-extra` to capture data needed later. [`wait`](wait.md) can be used when a script needs to wait for queued server work; do not depend on receiving a displayed 302 entry for every nickname.

## Options

| Option | Effect |
| --- | --- |
| `-direct` | Bypass the cache and query the server. |
| `-count number` | Change the number of nicknames per request from five. Use a positive count within the server's supported limit; the client does not validate it. |
| `-extra variable` | Save the variable's current value and append it to callback or normal-output hook arguments. Supply its name, not its expanded value. |
| `-cmd commands` | Use all remaining text as the callback body; put nicknames and other options before it. |
| `-flush` | Discard requests still waiting to be sent on this server. Already outstanding requests are not canceled. |

Options are case-insensitive. `-d`, `-co`, `-c`, `-e`, and `-f` are accepted abbreviations, respectively. Other tokens that are not accepted as nicknames or numbers are ignored. `-cmd` without preceding nicknames reports an error. A missing `-extra` variable reports an error and stops argument parsing, but already collected nicknames can still be processed.

The callback receives:

| Argument | Meaning |
| --- | --- |
| `$0` | Nickname |
| `$1` | `+` for an IRC operator, otherwise `-` |
| `$2` | `+` for away, otherwise `-` |
| `$3` | Username |
| `$4` | Hostname |
| `$5-` | Captured extra data, if supplied |

Unlike the normal 302 hook, the callback's away sign is `+` for away. Extra data belongs to the 302 hook, not numeric 303.

## Examples

```text
/userhost joebob jimbo
```

In a script, request fresh status and handle each result:

```text
userhost -direct joebob jimbo -cmd {
    echo $0 is $3@$4
    if ([$1] == [+]) echo $0 is an IRC operator
    if ([$2] == [+]) echo $0 is away
}
```

Capture data before an asynchronous request:

```text
assign context Requested from my script
userhost -extra context joebob jimbo -cmd {
    echo $0 is $3@$4 ($5-)
}
```

Later changes to `context` do not change the captured callback data.

## Restrictions and source

The built-in is always registered but silently does nothing unless the current server is registered. It has no function return value. Use this command rather than sending raw `USERHOST` with [`quote`](quote.md), because unsolicited 302 replies can interfere with its queue. Server limits, missing replies, or malformed responses can prevent normal completion.

[source/who.c](../../source/who.c) implements `userhostcmd()`, `userhostbase()`, queues, reply parsing, and callbacks. [source/numbers.c](../../source/numbers.c) routes numeric 302 to that queue. [source/server.c](../../source/server.c) initializes the outstanding-request limit; [source/commands.c](../../source/commands.c) registers the command.
