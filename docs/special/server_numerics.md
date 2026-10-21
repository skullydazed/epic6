# Hooking server numerics

IRC servers send numbered replies and errors, conventionally written with three digits. EPIC can register numbered [on](../commands/on.md) handlers as well as named events. Numeric meanings and fields vary by server implementation and negotiated features, so use the network's protocol documentation for the exact replies to an operation.

## Registering a numbered handler

Use the numeric in place of a named event:

```irc
on #-401 -5 "*" {
    echo Numeric 401 arguments: $*;
};
```

This adds a quiet handler at serial `-5` for numeric `401`. It prints the text supplied to that numbered hook. Serial groups, best-pattern selection, and noise modes use the normal `on` machinery; see [serial numbers](serial_numbers.md). The example does not send a request or provoke an error by itself.

For the ordinary numeric path, `$0` is the reporting server and `$1-` contains the remaining parameters, excluding the initial recipient nickname. IRC trailing-parameter colons and field boundaries are not preserved as a raw protocol line. Do not assume every numbered hook uses this layout: some numeric-specific handlers supply a special format. For example, `301` supplies the away nickname and message without the reporting server prefix.

## Processing and suppression

A numeric can update client state before a hook runs. Some replies are consumed by specialized request handlers or named events rather than reaching the generic numbered-hook path; `302` is handled by the userhost machinery and `303` by the ison machinery. An earlier suppressing [raw_irc](../on/raw_irc.md) handler can also stop normal protocol processing.

When a reply reaches the common path, the numbered hook runs first. If it permits default handling, [on numeric](../on/numeric.md) runs before the corresponding display branch. That named event receives the numeric as its first argument, then the reporting server and remaining reply fields. Its numeric is formatted without leading zero padding: `001` is supplied as `1` there.

A suppressing serial-zero hook can skip the later default branch, but it does not undo state changes already made or cancel the server's operation. Some later branches perform additional event processing as well as display. Inspect the specific numeric's handling when ordering or suppression matters; a numeric hook is not an unconditional interception point before every client action.

The [show_numerics](../set/show_numerics.md) setting changes numeric prefixes in applicable default output. It does not change which protocol reply arrived or make an absent reply occur.

## Common reply groups

The following are useful starting points, not an exhaustive cross-network specification:

| Request | Common replies |
| --- | --- |
| `whois` | `311`–`313`, `317`–`319`; networks can provide additional fields/replies. |
| Channel ban-list query | `367` entries and `368` end of list. |
| `trace` | Often `200`–`206`, `208`, `209`, `261`, `262`. |
| `stats` | Often `211`–`219` and `241`–`244`, depending on the requested report. |

Do not use the absence of an optional WHOIS or STATS field as a general completion signal. Use the operation's documented end reply or EPIC callback where available. Server permissions and privacy settings can restrict what is returned.

## Common error numerics

| Numeric | Conventional meaning |
| --- | --- |
| `401` | No such nickname. |
| `404` | Cannot send to the channel. |
| `421` | Unknown IRC command. |
| `436` | Nickname collision. |
| `465` | Connection rejected because of a server ban. |
| `473` | Cannot join an invite-only channel. |
| `474` | Cannot join because of a channel ban. |
| `482` | Insufficient channel-operator privileges. |

The explanatory text, parameter count, and consequences depend on the server. A nickname collision is not a universal promise of a particular kill or disconnect behavior. Unknown or malformed numerics can instead follow EPIC's odd-protocol handling.

## Source references

- `source/parse.c`: dispatch of incoming numeric commands.
- `source/numbers.c`: recipient removal, state updates, specialized paths, numbered hooks, `numeric`, and display.
- `source/hook.c` and `include/hook.h`: numeric event registration and shared hook rules.
- `source/who.c`: userhost/ison/WHO reply processing.
