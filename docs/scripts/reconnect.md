# reconnect script

## Synopsis

```text
/load reconnect
/set auto_reconnect on|off|toggle
/set auto_reconnect_delay <seconds>
/set auto_reconnect_retries <number>
/set auto_reconnect_try_other_servers on|off|toggle
/set auto_reconnect_delay_method on|off|toggle
/set auto_reconnect_join_delay <seconds>
/lsreconnects
/rmreconnect
```

## Description

This script handles reconnection after a lost IRC connection, including attempts to rejoin remembered channels in their former windows. It is intended to recover a connection while you are away from the client. It is script-provided recovery, not a guarantee that the server, authentication, channels, or windows can be restored.

Loading enables recovery by default. The script listens for [reconnect_required](../on/reconnect_required.md), announces the loss, and schedules a retry after the configured delay. On each retry it requests the server connection if that server has no open connection. If a connection is still open or being established, it announces that fact and schedules another check rather than starting a parallel attempt.

A server reaching `active` stops the group's reconnect timer and clears its failure counter. Channel restoration is then requested. The server still decides whether registration, authentication, and each join succeed.

## Settings

All settings below are created through [addset](addset.md), which is loaded by the script. Loading explicitly reapplies these defaults.

| Setting | Default | Meaning |
| --- | --- | --- |
| `auto_reconnect` | `on` | Install recovery hooks and commands. `off` removes the recovery hooks and named reconnect timers. |
| `auto_reconnect_delay` | `75` | Seconds before the first retry and between subsequently scheduled checks. |
| `auto_reconnect_retries` | `5` | Retry-count threshold used before changing servers or giving up. Use a positive value. |
| `auto_reconnect_try_other_servers` | `on` | Permit moving to the next server in an explicitly named group after the threshold. |
| `auto_reconnect_delay_method` | `off` | If off, send one combined channel/key join request; if on, schedule individual joins. |
| `auto_reconnect_join_delay` | `5` | Initial delay for individual joins; each following join is scheduled one second later. |

Integer settings accept nonnegative values, but zero retries is unsuitable because the handler uses that value as a modulus. A zero retry delay can produce rapid retries. Changing the delay does not move a timer already scheduled, but its new value is used when the handler schedules another check; it therefore can affect an ongoing recovery cycle. Join delay applies only to individual-join mode.

## Server groups and retries

Explicitly named server groups share recovery state and a timer. The script uses `serverctl(next_server_in_group)` to select the next configured server, wrapping through that group; if there is no alternative, that lookup can return the same server. Do not use simultaneous connections to multiple servers in one named group with this script.

For the `<default>` group, recovery state is tagged with the server name instead and no group switching is attempted. When switching is disabled or the group is `<default>`, reaching the stop branch prints instructions to reconnect manually and clears that failure counter.

Retry bookkeeping is not a simple independent limit per server: the handler uses a modulo counter plus `auto_reconnect.overflow`, and that overflow flag is shared across recovery groups and is not reset by the successful-connection hook. Attempts on different groups can affect when a switch or stop occurs. Do not interpret the displayed attempt number as a guaranteed count of failures on the current server.

Initial DNS failures or connection refusal do not by themselves start this script: it depends on the client's `reconnect_required` event, normally generated after loss of a previously registered connection. Once a cycle is running, its own timer can continue checking failed attempts. Manual disconnects and other paths that do not generate that event are not equivalent triggers.

## Channel restoration

The script remembers channels, cached keys, and window UUIDs when [channel_lost](../on/channel_lost.md) occurs while the server state is `closing`. It does not record every ordinary part or kick for rejoining. On `active`, the default mode sends one `join` request containing comma-separated channel and key lists. Individual-delay mode sends one channel per timer but **omits remembered keys**.

The saved channel/key/window lists depend on their positional alignment. Empty cached keys are appended through `push`, which omits empty values; a mixture of keyed and unkeyed channels can therefore misalign the key list. Keys are only as current as the local cache. Neither mode waits for a separate authentication-ready event before requesting joins.

The [channel_claim](../on/channel_claim.md) hook attempts to claim a returning channel for its saved window only if that window still belongs to the returning server. It then removes that channel's saved entries. Closing windows, moving them to other servers, changing server refnums, changing groups, or connecting multiple same-name entries can invalidate these assumptions. Channel failures are not individually retried by a dedicated rejoin loop.

## Commands and limits

`lsreconnects` lists decoded tags for pending `auto_reconnect.*` timers. These are group names, or server names for the default group; a pending timer does not necessarily mean a new connection attempt is currently executing.

`rmreconnect` cancels the named reconnect timer for the current window's server/group. It does not close an already open connection, disable future loss handling, clear saved channels, or cancel the separate unnamed individual-join timers. Turning `auto_reconnect` off likewise does not cancel those delayed joins.

The disable branch contains `alais -lsreconnects`, a misspelling of `alias`, so the listing alias is not properly removed and an unknown-command diagnostic or request may result. The other recovery command aliases are removed correctly. Serial-100 hooks can conflict with other scripts using the same patterns. No external program or optional platform feature is required; [builtins](builtins.md) must already supply `encode`, `decode`, and `winnum`.

## Example

```text
/load reconnect
/set auto_reconnect_delay 90
/set auto_reconnect_retries 3
/lsreconnects
```

To cancel a pending cycle, select a window associated with that disconnected server and use `/rmreconnect`. To stop automatic recovery more broadly, use `/set auto_reconnect off`. These operations have no function-return contract; status and errors are displayed as client messages.

## History

Automatic reconnect and channel rejoin were built into EPIC4 and moved to scripts during EPIC5's nonblocking-connection work. The earlier script by nullie and kreca appeared in EPIC5-0.3.1. The current implementation credits zlonix with help from hop and testing by skered; its source records revisions in September 2021, February 2022, and September 2022 and places it in the public domain.

## Source references

- [script/reconnect](../../script/reconnect): settings, retry handler, group tags, channel preservation, cleanup, and commands.
- [script/addset](../../script/addset) and [script/builtins](../../script/builtins): setting creation and helper dependencies.
- [source/server.c](../../source/server.c): reconnect-event dispatch, open-state queries, and next-server-in-group selection.
- [source/names.c](../../source/names.c): channel-loss events before removing cached membership.
- [source/timer.c](../../source/timer.c): timers are removed from the pending list before their handler executes and can schedule their successors.
- [source/window.c](../../source/window.c): window queries and channel claims.
- [source/functions.c](../../source/functions.c): `push` omits empty additions; list functions manage saved entries.
