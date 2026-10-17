# on encrypted_privmsg (removed)

## Former synopsis

```text
/on [<noise>]encrypted_privmsg "<pattern>" { <commands> }
```

## Description

The `encrypted_privmsg` event formerly identified incoming [messages](../commands/msg.md) handled by the client's end-to-end encryption support, historically including CTCP SED payloads. Allowing default handling continued to [on general_privmsg](../on/general_privmsg.md). Scripts could replace that display with an encrypted-message marker.

EPIC6 has removed this named hook and its former end-to-end encryption path. It does not decrypt a message through that implementation or generate an `encrypted_privmsg` event. The former ordering relative to ignore and flood checks no longer describes a current event.

## Former parameters

| Parameter | Former meaning |
| --- | --- |
| `$0` | Sender's nickname. |
| `$1` | Message target, described as the sender or channel. |
| `$2-` | Received message. |

These describe the removed interface, not arguments supplied by EPIC6. Suppressing this hook is no longer a way to control message display or identify encrypted content.

## Compatibility

Attempting to register `/on encrypted_privmsg ...` is rejected with `No such ON function: ENCRYPTED_PRIVMSG`. The former example that added a crypt marker cannot run through this hook, and its historical automatic-reply restrictions are not a current hook contract.

For ordinary incoming messages, use [on general_privmsg](../on/general_privmsg.md) and the appropriate specific event, such as [on msg](../on/msg.md) or [on public](../on/public.md). They do not promise decrypted content or establish that a message was encrypted. CTCP payloads pass through [on ctcp_request](../on/ctcp_request.md) and CTCP processing before remaining ordinary text reaches these message hooks. Current CTCP initialization does not register the former SED handler.

Scripts implementing their own encryption must provide their own processing and display behavior; the removed hook is not a built-in integration point for them.

## Source references

- [KNOWNBUGS](../../KNOWNBUGS): records removal of `on encrypted_(privmsg|notice)`, SED and related encryption types, and the end-to-end encryption implementation.
- [source/hook.c](../../source/hook.c): the hook table omits `encrypted_privmsg`; `find_hook` rejects unknown named events.
- [include/hook.h](../../include/hook.h): current hook identifiers contain no encrypted-message event.
- [source/parse.c](../../source/parse.c): `p_privmsg` processes CTCP requests and then ordinary message hooks without the former encrypted-message stage.
- [source/ctcp.c](../../source/ctcp.c): current initialization registers only the action handler, not the former SED handler.
