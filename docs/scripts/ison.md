# ison script

## Synopsis

```text
/load ison
/ison [-server <server>] [-next] [-now] [-len <number>] [-f] [-oncmd {<commands>}] [-offcmd {<commands>}] [-end {<commands>}] <nick> [<nick>...]
$isonctl(get <server-refnum> maxlen|maxreq|sendqueue)
$isonctl(set <server-refnum> maxlen|maxreq|sendqueue <integer>)
```

## Description

ISON asks the connected IRC server which nicknames from a supplied list it currently reports online. This script implements its own request queue and callbacks over `quote ison`, replacing the normal [ison](../commands/ison.md) command alias when loaded. It does not establish a connection or query an arbitrary remote server across the network. `-server` selects an existing client connection.

The server determines visibility and returned nickname spelling. An empty reply means none of the requested names were reported online, not an independent guarantee of their availability. Unlike the C command, this alias does nothing when no nicknames are supplied; it does not default to your nickname.

Without callbacks, replies are printed as `online: <names>` at the `crap` output level. With callbacks, the supplied commands receive the returned online names or calculated offline names as `$*`. Requests can be split into several protocol messages, so callbacks and displayed lists can arrive in pieces rather than as one combined result.

## Options

| Option | Script behavior |
| --- | --- |
| `-server <server>` | Resolve a server selector and use that connection. If the script detects an invalid or unconnected selection, it prints `error: invalid server ...` and falls back to the current server context. |
| `-next` | Prepend generated requests to the pending send queue instead of appending. Multiple chunks are individually prepended, so their order can reverse. |
| `-now` | Send generated requests immediately, bypassing the pending-queue concurrency limit. They are still recorded as outstanding requests. |
| `-len <number>` | Override the script's nickname-list length threshold for this call. This is not validation of the full IRC wire-line length. |
| `-f` | Clear the current script queue lists before adding new work. It does not cancel requests already sent to the server or clear the C command's separate queue. |
| `-oncmd {<commands>}` | Run with online names for each reply, including an empty argument list when none were reported. |
| `-offcmd {<commands>}` | Run with the requested names not in that reply. |
| `-end {<commands>}` | Run without nickname arguments on the reply marked as the final generated request. |

Callback blocks must be parseable by `shiftbrace`; otherwise the script prints `error: could not parse ... expression` and continues parsing. The implementation creates temporary callback aliases and removes them on its marked final reply. Lost replies or a queue flush can leave callback definitions or request metadata behind.

The script does not implement the C command's `-n` and `-s` options: unrecognized words are added to the nickname list. Use `//ison` when you explicitly need the C implementation and its documented options. The current bundled `notify` script uses server MONITOR support, not this script as its notification engine.

## Queue controls

On load, ordinary script variables initialize global defaults to `maxlen = 500`, `maxreq = 2`, and `sendqueue = 1`. These are not `/set` settings. Per-server overrides can be read or assigned through `isonctl`. For `set`, a negative server refnum changes the global default; for `get`, a missing per-server value falls back to that default.

`maxlen` controls request splitting, `maxreq` limits outstanding requests while queueing is enabled, and zero `sendqueue` is intended to dispatch without that limit. Changing `maxreq` or `sendqueue` also invokes pending-request dispatch. Set operations accept an integer but define no explicit success return; invalid values or unknown controls return empty. The script does not impose sensible positivity or boolean bounds, so use positive lengths/counts and `0` or `1` for the queue switch.

The default length calculation omits some inserted spaces and the IRC command/framing overhead. A nickname at least as long as the threshold can cause an empty chunk. Server and client line-size restrictions still apply. The source also contains stray closing parentheses in its explicit server-lookup and queue-disabled dispatch expressions; those branches should not be treated as a validated replacement for the built-in queue.

The `ison` alias returns the number of request chunks it generated when called as a function. This is not an online-user count or an acknowledgement that those chunks reached the server. `flush_ison_queues` clears script queue lists for known servers, and loading the script invokes it immediately. A numeric `003` hook also clears the lists for that server during registration.

## Reply ordering and hook interactions

The script installs a suppressing serial-zero `303` hook and serial `555` outgoing ISON observers. The observer tries to recognize requests not created by this script and records a `-1` marker so their replies receive ordinary online-list output instead of script callbacks.

The C ISON machinery processes numeric `303` before invoking the numbered hook. Mixing outstanding C requests, this script's raw quoted requests, and other ISON consumers can misassociate replies, because both queue managers rely on reply order. Manually clearing a queue cannot retract a server reply still in transit. The script also has only one `last_ison_req` value per server, not a request identifier carried over IRC.

Do not suppress incoming `303` through `raw_irc` before the C parser handles it: that prevents queue completion and can leave later requests stalled or mismatched. Other scripts replacing the numbered hook or intercepting outgoing lines can also disrupt this manager. It has no retry or timeout recovery that guarantees callback completion.

## Example

```text
/load ison
/ison Alice Bob
```

This queues a lookup on the current server and displays the reported names when replies arrive. For applications needing the current C callbacks, prefer the separate built-in command documented under [ison](../commands/ison.md), accounting for the script's hooks if it remains loaded.

## Source references

- [script/ison](../../script/ison): parser, controls, request splitting, generated callbacks, and serial hooks.
- [source/who.c](../../source/who.c): C ISON queue and `303` processing before numbered-hook dispatch.
- [source/numbers.c](../../source/numbers.c): incoming `303` routing.
- [source/hook.c](../../source/hook.c): serial groups and serial-zero suppression.
- [source/commands.c](../../source/commands.c) and [source/server.c](../../source/server.c): quoted protocol sends and selected server context.
- [script/notify](../../script/notify): current MONITOR-based notification wrapper.
