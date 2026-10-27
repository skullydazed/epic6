# notify command

## Synopsis

```irc
/load notify
/notify
/notify -
/notify <nickname>
/notify +<nickname>
/notify -<nickname>
```

## Current implementation

The former C built-in notify command is removed in EPIC6. The supplied [`notify` script](../scripts/notify.md), also loaded by [`builtins`](../scripts/builtins.md), defines the command as a wrapper around the current server's MONITOR facility. It does not use the old ISON polling system or maintain a separate local nickname list.

The script checks the current server's advertised `005 MONITOR` value. If it is false or absent, it reports that MONITOR is unsupported and sends no request. There is no WATCH or ISON fallback.

| Input | Request sent |
| --- | --- |
| `/notify` | `MONITOR s`, requesting the status of watched names. |
| `/notify -` | `MONITOR c`, clearing that connection's server-side watch list. |
| `/notify alice` or `/notify +alice` | `MONITOR + alice`. |
| `/notify -alice` | `MONITOR - alice`. |

The server controls nickname validity, watch-list limits, replies, and notification timing. A request does not confirm that a name was accepted. The intended watch targets are nickname identities, not wildcard userhost masks; the alias does not validate them itself.

For multiple words, the alias separates additions from removals but sends only the additions when both groups are present. It joins names with spaces rather than constructing comma-separated nickname lists. Thus `/notify scuba diver` emits `MONITOR + scuba diver`, and the server determines how the extra parameter is handled. Use separate single-name commands to avoid that ambiguity. The script's `/monitor <arguments>` alias forwards MONITOR arguments directly without the advertised-support check.

## Replies and differences from the old command

The script installs numeric 730 and 731 handlers that display the received text as online or offline notifications. It does not maintain an independently queryable online/offline cache, generate the old notify signon/signoff hooks, or automatically restore its watch list after reconnecting.

The original command's bare `+` form displayed only online names. That behavior is not implemented by the current wrapper: `+` is parsed as an addition with an empty name, not a status-list selector. With no arguments, the new wrapper asks the server for watched-name status rather than printing a locally stored list.

A bare `-` still requests list clearing, but the current script supplies no saved-list recovery facility. The original page's warning that suppressing numeric 303 irreparably breaks notify referred to the retired ISON-based implementation. The current wrapper uses MONITOR replies instead; it does not depend on 303 for this operation. Other scripts using ISON can have their own requirements.

Invoking the supplied alias as `$notify(...)` reaches an unfinished function branch that returns no value and sends no request. See [notify interface](notify.md) for that distinction.

## Examples

```irc
/notify scuba
/notify diver
/notify -buddha
/notify
/notify -
```

These request two additions separately, remove a name, request current status, and finally clear the connection's watch list. They replace the original examples' unsupported assumption that a bare `+` displays online names.

## History

The original DokuWiki page described the former built-in interface. Its revision identifier was `notify_command.txt,v 1.2 2006/08/01 05:32:26 sthalik Exp`. The supplied MONITOR wrapper is marked as hop's 2025 implementation.

## Source references

- [script/notify](../../script/notify): command forms, support check, argument grouping, direct MONITOR alias, and numeric hooks.
- [script/builtins](../../script/builtins): notify script loading.
- [source/commands.c](../../source/commands.c) and [source/functions.c](../../source/functions.c): current registries omit the former built-in notify interfaces.
- [source/server.c](../../source/server.c): current-server reference resolution and `serverctl()` access to `005 MONITOR`.
- [source/numbers.c](../../source/numbers.c): numeric processing and hook arguments.
