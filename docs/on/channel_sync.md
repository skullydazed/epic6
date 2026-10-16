# on channel_sync

## Synopsis

```text
/on [<noise>]channel_sync "<pattern>" [(<argument list>)] { <commands> }
```

## Description

Triggered when EPIC finishes its initial channel synchronization after joining. This is the usual point to query cached membership, operator status, and userhost information rather than assuming those caches are ready immediately after the join event.

Completion means that EPIC has finished the synchronization work it chose to perform; it does not guarantee that every possible piece of channel information is available. Server responses, anonymous-channel behavior, and the configured cache limit affect what was collected. The cache remains subject to later joins, departures, and mode changes.

Use the standard [on](../commands/on.md) matching and noise controls. The pattern matches all three fields below together. There are no event-specific options.

## Parameters

| Parameter | Meaning |
| --- | --- |
| `$0` | Channel synchronized. |
| `$1` | Elapsed synchronization time in seconds, formatted as a floating-point number. |
| `$2` | Server refnum. |

The elapsed time starts when EPIC creates or resets its channel state after receiving your join, not when you first send a join request. The channel is marked as no longer syncing before this hook runs.

## What synchronization collects

On your join, EPIC requests the channel's modes. It caches the initial names replies while synchronization is pending. On receiving the mode reply, it normally issues a `who` request to collect userhost information, and invokes this event when that request ends.

For an anonymous channel, EPIC completes synchronization at the mode reply without that `who` request. It also skips `who` when the cached channel size exceeds the server's `maxcachesize` value configured through [serverctl](../functions/serverctl.md). The default is `-1`, which imposes no size limit; a nonnegative value allows `who` only when the cached member count is at most that value. In these cases the event can occur without complete userhost information.

Completion depends on receiving and processing the relevant server replies. This hook does not establish that unrelated data such as ban lists has been requested or cached.

## Default behavior

There is no built-in output for this event. Suppressing its default handling does not undo synchronization or discard the cache. Running a handler does not itself send an IRC request. Its default output context is the channel at the `other` message level.

## Example

```text
on ^channel_sync * (channel, elapsed, server) {
    echo Synced $channel on server $server in $elapsed seconds;
};
```

## Source references

- [source/parse.c](../../source/parse.c): `p_join` handles your join and requests channel modes.
- [source/names.c](../../source/names.c): `add_channel` starts the timer; `channel_not_waiting` clears the syncing flag and invokes the hook.
- [source/numbers.c](../../source/numbers.c): numeric 353 caches initial names; numeric 324 chooses whether to issue `who`; `add_user_who` caches userhosts and `add_user_end` completes synchronization.
- [source/server.c](../../source/server.c): server initialization sets the default cache limit; `serverctl` exposes `maxcachesize`.
- [source/hook.c](../../source/hook.c): event registration and `do_hook` implement standard hook matching.
