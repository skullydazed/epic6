# on channel_claim

## Synopsis

```text
/on [<noise>]channel_claim "<pattern>" [(<argument list>)] { <commands> }
```

## Description

Triggered while EPIC adds or recreates a channel record, before assigning its window. This lets scripts choose which window receives the channel.

Use standard [on](../commands/on.md) matching, serial numbers, and noise controls. The pattern matches the complete event data.

## Parameters

| Parameter | Meaning |
| --- | --- |
| `$0` | Server refnum. |
| `$1` | Channel name. |
| `$2` | Proposed window reference, using the internal window identifier rather than necessarily its user-visible refnum. |

## Behavior

EPIC proposes the previous window when available, otherwise a window waiting for the channel, otherwise the server’s current window. During the hook, [window claim](../window/claim.md) can nominate a window on the same server: `window <window> claim <channel>`. The last successful claim wins. Claiming is allowed only during this interval and must match the channel and server.

The hook’s suppression result is ignored; suppression cannot cancel the addition or window assignment. This occurs before channel synchronization finishes; see [on channel_sync](channel_sync.md).

## Example

```text
on -channel_claim "% #support *" {
    echo Assigning $1 on server $0;
};
```

## Source references

- [source/names.c](../../source/names.c): `add_channel` selects the proposed window; `window_claims_channel` records overrides.
- [source/window.c](../../source/window.c): `windowcmd_claim` implements the claim command.
- [source/hook.c](../../source/hook.c): event registration and standard hook dispatch.
