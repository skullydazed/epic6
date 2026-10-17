# on encrypted_notice (removed)

## Former synopsis

```text
/on encrypted_notice "<pattern>" { <commands> }
```

## Description

The `encrypted_notice` event formerly identified incoming [notices](../commands/notice.md) handled by the client's end-to-end encryption support, historically including CTCP SED payloads. Allowing default handling continued to [on general_notice](../on/general_notice.md); scripts could replace that display with a message identifying the notice as encrypted.

EPIC6 has removed this named hook and its former end-to-end encryption path. It does not decrypt a notice through that implementation or generate an `encrypted_notice` event. The former placement relative to ignore and flood checks no longer describes a current event.

## Former parameters

| Parameter | Former meaning |
| --- | --- |
| `$0` | Sender's nickname. |
| `$1` | Message target, described as the sender or channel. |
| `$2-` | Received message. |

These describe the removed interface, not arguments supplied by EPIC6. Suppressing this hook is no longer a way to control notice display or identify encrypted content.

## Compatibility

Attempting to register `/on encrypted_notice ...` is rejected with `No such ON function: ENCRYPTED_NOTICE`. The former example that added an encrypted marker cannot run through this hook.

For ordinary incoming notices, use [on general_notice](../on/general_notice.md), [on notice](../on/notice.md), or [on public_notice](../on/public_notice.md), as appropriate. These events do not promise decrypted content or prove that a message was encrypted. CTCP payloads in notices pass through the CTCP reply path before ordinary notice handling; see [on ctcp_reply](../on/ctcp_reply.md).

The former automatic-reply restriction attached to this removed hook is not a current hook contract. Consult the active notice and command documentation for their behavior; the built-in `ctcp` command specifically rejects invocation while processing a notice.

## Source references

- [KNOWNBUGS](../../KNOWNBUGS): records removal of `on encrypted_(privmsg|notice)`, SED and related encryption types, and the end-to-end encryption implementation.
- [source/hook.c](../../source/hook.c): the hook table omits `encrypted_notice`; `find_hook` rejects unknown named events.
- [include/hook.h](../../include/hook.h): current hook identifiers contain no encrypted-notice event.
- [source/parse.c](../../source/parse.c): `p_notice` processes CTCP replies and then ordinary notice hooks without the former encrypted-notice stage.
- [source/ctcp.c](../../source/ctcp.c): current initialization registers only the action handler, not the former SED handler.
- [source/commands.c](../../source/commands.c): `ctcpcmd` rejects CTCP commands invoked from a notice.
