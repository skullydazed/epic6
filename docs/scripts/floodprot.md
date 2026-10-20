# floodprot

## Synopsis

```text
/load floodprot
/set floodprot_burst [<count>]
/qflush
```

## Description

This script queues outgoing IRC lines to reduce bursts, such as those caused by pasting text. It installs a conditional [send_to_server](../on/send_to_server.md) hook, keeps a separate named array for each server's queued lines, and starts a general repeating timer named `_floodprot` with a three-second interval. It does not guarantee that a server will accept the resulting traffic or that you cannot be disconnected.

It requires `addset` and `myservers` from the usual [builtins](builtins.md) environment; load `builtins` first if your startup configuration does not provide them. Loading the script initializes `floodprot_burst` to `4`, replaces the named timer through `timer -update`, and installs the hooks and `qflush` alias. It does not purge queues already stored from an earlier load.

## Burst setting and scheduling

`floodprot_burst` is a script-defined integer setting. With no value, `set` displays its current value. The initial value is `4`; assigning zero changes it back to `4`. The callback also sets every currently connected server's counter to the chosen count plus one, so those servers begin queueing their next ordinary line immediately. This intentionally prevents new input from overtaking already queued lines.

For ordinary outgoing traffic, the hook behaves as follows:

- An unseen server counter is initialized to `1`, and that line is allowed through.
- While the counter is less than or equal to the setting, another line is allowed and the counter increments.
- Otherwise the prepared line is appended to `queue-<server-refnum>` and the original send is suppressed.

This inclusive comparison means an initially unseen server can send five ordinary lines with the default setting of four before queueing, rather than exactly four. Every three seconds, each connected server with a nonempty queue has one line removed and resubmitted. Its counter is set temporarily to `-1` to permit that replay, then set to the burst count plus one. With no queued line, a positive counter decreases by one per timer pass.

The queue is first-in, first-out for each server. It is not a timer per message, nor a precise byte-rate limit. Timer scheduling and other hooks can affect actual delivery. Lines beginning with the IRC command `PING` or `PONG` bypass the counter and queue entirely.

## Flushing and disconnection

`qflush` discards queued lines for all servers currently reported connected by `myservers(0)` and prints `Flushed queue for server <refnum>` for each nonempty queue. It does not send the discarded lines. It does not inspect disconnected-server queues.

The alias's counter-reset expression uses `$0`, not its loop's server variable. With the documented no-argument invocation, it therefore does not reliably reset the counters of the flushed servers. New traffic may still queue until subsequent timer passes reduce a counter. Arguments are not a supported server selector.

A [server_lost](../on/server_lost.md) hook deletes that server's queue and counter during the normal hard-close path. This event is not guaranteed for every kind of reset or already-closed connection. Queued lines are held only in client memory and are not a persistent delivery mechanism.

## Caveats

The hook handles prepared protocol lines, not just chat messages. Other protocol commands can be delayed too; only `PING` and `PONG` are explicitly exempt. Local echoes and caller-side updates may already have occurred before the actual network send is suppressed. Queueing does not imply successful delivery or server acceptance.

The current send hook receives already encoded, mangled, length-limited text with line terminators. The script stores that text and replays it through `xquote` after removing one trailing character with `chop`. The ordinary formatted sender then applies its processing again. This is not a transparent raw-byte relay, particularly for non-UTF-8 encodings or protocol framing. Other hooks that suppress the replay can also lose the line, since it is removed from the queue first.

Very long lines can still be truncated or rejected, and repeated traffic can grow the queue without a configured size limit. A backlog can delay commands long after the action that generated them. Do not treat this example script as comprehensive protection against a server's flood or registration limits.

## Example

```text
/load floodprot
/set floodprot_burst 4
```

This enables the queueing hook and timer. To discard pending lines later, use `/qflush`, accounting for its counter-reset limitation above. The script has no separate on/off setting.

## Source references

- [script/floodprot](../../script/floodprot): setting callback, counters, queue arrays, timer, disconnect cleanup, and `qflush`.
- [script/builtins](../../script/builtins): `myservers` and `addset` support.
- [source/server.c](../../source/server.c): prepared-line formatting and `send_to_server` hook dispatch.
- [source/commands.c](../../source/commands.c): `xquote` sends through the formatted sender.
- [source/timer.c](../../source/timer.c): general repeating timers and named updates.
- [source/vars.c](../../source/vars.c): setting callbacks and prevention of recursive callback invocation.
