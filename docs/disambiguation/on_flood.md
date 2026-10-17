# on flood (removed)

## Former synopsis

```text
/on [<noise>]flood "<pattern>" { <commands> }
```

## Description

The `flood` event formerly ran when the client's incoming-message flood detector identified many messages arriving within a short interval. A burst could reflect IRC delivery delays rather than deliberate flooding by the sender.

EPIC6 has removed this named hook and the former incoming flood-control path. It does not generate a `flood` event or apply the former hook-controlled suppression of the message that triggered detection. The historical `flood_warning` behavior is not a current event contract.

## Former parameters

| Parameter | Former meaning |
| --- | --- |
| `$0` | Nickname identified by the detector. |
| `$1` | Detected flood type. |
| `$2` | Channel, if applicable. |
| `$3` | Number of messages in the detected flood. |
| `$4-` | Triggering message content. |

These describe the removed interface, not arguments supplied by EPIC6.

## Compatibility

Attempting to register `/on flood ...` is rejected with `No such ON function: FLOOD`. The former examples that temporarily ignored a detected sender or exempted network services cannot run through this hook. Scripts needing incoming rate tracking must implement their own counting and decisions using the relevant message events, such as [on general_privmsg](../on/general_privmsg.md) and [on general_notice](../on/general_notice.md).

The CTCP sending code retains a separate outbound reply-throttling branch. It does not invoke `flood` and is not a replacement for the removed incoming detector.

## Source references

- [KNOWNBUGS](../../KNOWNBUGS): records removal of the previously sunset `flood` hook.
- [source/hook.c](../../source/hook.c): the named-hook table omits `flood`; `find_hook` rejects unknown event names.
- [include/hook.h](../../include/hook.h): current hook identifiers contain no flood event.
- [source/parse.c](../../source/parse.c): incoming messages use ordinary message and notice hooks without a flood-event stage.
- [source/ctcp.c](../../source/ctcp.c): `send_ctcp` contains separate outbound reply-throttling code without a flood-hook invocation.
