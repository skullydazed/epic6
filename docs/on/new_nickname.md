# on new_nickname

## Synopsis

```text
/on [<noise>]new_nickname "<pattern>" [(<argument list>)] { <commands> }
```

## Description

Triggered when EPIC processes a nickname rejection before registration and needs a replacement nickname to continue connecting. A handler is expected to use [nick](../commands/nick.md) to submit another choice. The server still decides whether that replacement is acceptable.

This is not a successful-nickname-change event. Rejection after the server is already registered follows a different path: EPIC restores the nickname reported by the server and does not invoke this hook. See [on nickname](nickname.md) for observed nickname changes.

Use standard [on](../commands/on.md) matching and noise controls. The pattern matches all three fields below together. There are no event-specific options.

## Parameters

| Parameter | Meaning |
| --- | --- |
| `$0` | Server refnum requiring a new nickname. |
| `$1` | Current accepted nickname, or `*` when none is recorded. |
| `$2` | Pending requested nickname, or `*` when none is recorded. |

Do not treat `$0` as a hostname. The hook runs while processing the rejection from that server, so a normal `nick <replacement>` command uses its server context. Use the pending value when deciding which requested nickname was rejected; it can differ from the accepted nickname.

## Default behavior and limitations

EPIC does not automatically choose a replacement here. After the hook returns, if no pending nickname is recorded or the pending nickname is unchanged from its earlier nonempty value, EPIC prints instructions to use `/NICK` to continue connecting and `/server +<refnum>` if reconnection becomes necessary.

The caller ignores the hook's default-handling return value. A suppressing hook does not suppress these instructions: changing the pending nickname through `nick` is what satisfies the usual replacement check. Merely displaying a suggestion does not submit it. The hook also does not cancel the server rejection or guarantee continued connection.

The rejection path handles several server numerics, including `432`, `433`, `438`, `439`, and `453`, plus a relevant `437` case. A pre-registration `439` addressed exactly to the requested pending nickname is treated as informational and bypasses this rejection path. Server wording and policy vary.

The hook is marked nonrecursive, so nested dispatch does not re-enter its handlers. A later server rejection can generate a new event after the current handler has finished.

## Example

Use a deliberately chosen alternate nickname for one specific pending choice:

```text
on ^new_nickname "% * example" {
    nick example_alt;
};
```

This avoids repeatedly submitting the same rejected nickname; if the alternate is rejected, this pattern does not select it again.

## History

This hook first appeared in EPIC5-0.0.3.

## Source references

- [source/server.c](../../source/server.c): `nickname_change_rejected` distinguishes registered servers; `reset_nickname` invokes the hook and checks whether the pending choice changed; `change_server_nickname` records and sends replacements.
- [source/numbers.c](../../source/numbers.c): nickname-rejection numeric branches invoke the rejection path, including the informational `439` exception.
- [source/commands.c](../../source/commands.c): the nickname command calls `change_server_nickname`.
- [source/hook.c](../../source/hook.c): the event registration sets the nonrecursion flag; dispatch implements standard matching.
