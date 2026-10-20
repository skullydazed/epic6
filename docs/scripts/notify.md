# notify script

## Synopsis

```text
/load notify
/notify [<nickname>|+<nickname>|-<nickname> ...]
/notify -
/monitor <arguments...>
```

## Description

This is the script-provided nickname notification interface. It replaces the former built-in `notify` command with a wrapper around the current IRC server's MONITOR facility. The supplied [builtins](builtins.md) script loads it automatically; `/load notify` also loads it explicitly.

The server maintains the watch list for that connection and reports online and offline names. This script does not maintain global, group, or local client-side lists, poll with ISON, or restore a saved list after reconnecting. It provides neither `nlist` nor the former `nickname:refnum` and `nickname::group` selection syntax. Use the desired connection's command context before changing its list.

## Commands and effects

The `notify` alias first checks the current server's advertised `005 MONITOR` value. If it is empty or false, it prints `This server does not support MONITOR which is used by /NOTIFY` and sends nothing. There is no WATCH or polling fallback.

| Input | Request sent to the current server |
| --- | --- |
| `/notify` | `MONITOR s`, requesting current watched-name status. This is not a client-side list display. |
| `/notify -` | `MONITOR c`, clearing the server's watch list. |
| `/notify alice` or `/notify +alice` | `MONITOR + alice`. |
| `/notify -alice` | `MONITOR - alice`. |
| `/monitor <arguments>` | `MONITOR <arguments>` directly, without the advertised-support check. |

For other arguments, leading `-` marks removals and leading `+` is stripped from additions; unprefixed words are additions. If a single command contains both additions and removals, **only additions are sent**. Submit them separately.

The alias joins multiple supplied words with spaces, not commas, and does not split requests to fit server limits or validate nickname syntax. For example, `/notify alice bob` sends `MONITOR + alice bob`; how extra parameters are interpreted depends on the server. Single-name operations avoid that ambiguity. The direct `monitor` alias lets you supply the server's required argument format yourself.

All requests are forwarded through [quote](../commands/quote.md). A request is not confirmation of success: support, list capacity, accepted syntax, results, and notification timing depend on the server. The script has no local retry, error recovery, persistence, or limit handling.

## Notifications and compatibility

The script installs serial-zero suppressing numeric hooks. Numeric `730` prints `*** <reply text> is online`; numeric `731` prints `*** <reply text> is not online`. The reply text is forwarded after removing the hook's server-name field, without parsing individual comma-separated entries or userhosts. Other numerics, including server errors, retain ordinary numeric handling unless another script changes it.

These hooks display replies directly rather than generating the removed notify signon/signoff events. The former `notify`, `notify_interval`, and `notify_userhost_automatic` settings do not control this implementation. Other scripts replacing the same hooks or aliases can change its behavior.

The alias can be invoked as `$notify(...)`, but that branch is an unimplemented placeholder: it returns empty and sends no request. It does not return a watch list or online status. Command use also has no status-return contract; replies arrive asynchronously from the server.

No external program or optional client platform feature is required. A usable server connection and server MONITOR support are needed for notifications. Loading the script itself defines aliases and hooks without starting monitoring.

## Example

```text
/notify alice
/notify +bob
/notify
/notify -alice
/notify -
```

This adds two names with separate requests, asks for current status, removes one name, then clears the connection's watch list.

## Other notes

The retained implementation is marked as 2025 work by hop. It is a different implementation from the older scripted notify system with global and local lists.

## Source references

- [script/notify](../../script/notify): aliases, support check, argument bucketing, placeholder function branch, and numeric hooks.
- [script/builtins](../../script/builtins): automatically loads `notify`.
- [source/server.c](../../source/server.c): `serverctl(get -1 005 monitor)` reads the current server's advertised support; `get_server` resolves `-1` to the current context.
- [source/commands.c](../../source/commands.c): `quotecmd` forwards the request.
- [source/numbers.c](../../source/numbers.c): generic numeric hooks receive server name followed by reply arguments, with default display when not suppressed.
