# ison

Query which nicknames are currently online on your IRC network.

## Synopsis

```text
/ison [options] [nickname ...]
```

## Description

`ison` sends queued IRC `ISON` requests to the server in the current command context. The server replies with the subset of requested nicknames that are online. With no arguments at all, EPIC queries your own nickname. Options alone do not automatically add your nickname.

The command is asynchronous, not a function returning a list. Normally each reply displays `Currently online:` followed by the online names, subject to the numeric `303` hook. If none are online, the list is empty. A request requires a registered server connection; otherwise the built-in silently does nothing. It is always registered.

The nickname list is whitespace-delimited and split into batches using a per-server length limit. A single nickname whose byte length reaches or exceeds that limit is skipped. By default one request is outstanding at a time; a reply removes the oldest outstanding entry and permits another pending request to be sent. There is no automatic timeout in this queue.

You cannot select a remote server with this command: all non-option words are treated as nicknames for the current network. Accepted names and the information returned depend on the server.

## Options

Options are case-insensitive and precede the nickname list. Unknown leading hyphen options are ignored.

| Option | Effect |
| --- | --- |
| `-n` | Insert new batches at the head of the pending queue instead of the tail. Already outstanding requests remain ahead of them. |
| `-s` | Attempt to send one pending request immediately, still respecting the outstanding-request limit. Normal command processing also attempts to advance the queue. |
| `-len number` | Set the server's batching limit in bytes, default `500`, with a minimum of `100`. This persists for later requests on that server. |
| `-max number` | Set the server's maximum outstanding requests, default `1`. Zero removes this limit. Use a nonnegative value. |
| `-oncmd {commands}` | Run the block with the online names from each batch as `$*`, only when that subset is nonempty. |
| `-offcmd {commands}` | Run the block with the offline names from each batch as `$*`, only when that subset is nonempty. |
| `-end {commands}` | Run the block when the last batch created by this invocation receives its reply. It receives an empty argument string. |
| `-d` | Display outstanding and pending queue entries for diagnosis. |
| `-f` | Discard all outstanding and pending entries for this server. Already transmitted requests cannot be recalled. |
| `-e` | Stop processing this invocation if the server already has a pending, unsent request. Outstanding requests alone do not trigger this check. |

Callback arguments require braces. A missing numeric or callback argument produces a diagnostic but does not necessarily cancel the rest of the command. Numbers are converted permissively rather than strictly validated. `-len` limits the nickname portion, not the complete IRC wire line; increasing it beyond the server's protocol limits can make requests fail.

Providing either `-oncmd` or `-offcmd` suppresses the normal reply display for those batches. Providing only `-end` retains it. Callback blocks execute in their own callback context with response arguments, rather than preserving the calling alias's locals. A split request can invoke the online and offline blocks several times. Do not use `-end` as an aggregate result callback: its `$*` does not contain the results. Priority insertion with `-n` can reorder pending batches, so the last-created batch need not be the last one answered when requests are split.

## Queue restrictions

Replies are matched to queue entries in order, without a server-provided request identifier. Suppressing normal handling of numeric `303` through `on raw_irc`, sending untracked `ISON` requests through [quote](quote.md), or flushing outstanding entries can disrupt that correspondence and any scripts sharing the queue. Numeric `303` hooks used after queue processing are different from intercepting the raw reply.

Scripts implementing notification lists can use this facility, but notification behavior is not part of this built-in. Do not wait indefinitely for a reply: missing responses can also prevent pending requests from advancing under the default limit.

## Examples

Query your own nickname:

```text
/ison
```

Display online and offline subsets explicitly:

```text
ison -oncmd {echo Online: $*} -offcmd {echo Offline: $*} -end {echo Final batch answered} alice bob carol
```

Inspect the current server's queue:

```text
/ison -d
```

## Source

Registration is in [source/commands.c](../../source/commands.c). `isoncmd`, option parsing, batching, queues, and callbacks are in [source/who.c](../../source/who.c). Defaults and server connection state are in [source/server.c](../../source/server.c). Numeric `303` dispatch is in [source/numbers.c](../../source/numbers.c), and raw-message interception is in [source/parse.c](../../source/parse.c).
