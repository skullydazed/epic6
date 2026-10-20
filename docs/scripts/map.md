# map script

## Synopsis

```text
/load map
/map [<server>]
```

## Description

This script displays a topological map of the IRC network, normally centered on the connected server. It can construct a client-side map from [links](../commands/links.md) replies when the server does not advertise its own MAP facility. It requires `encode` and `servernum` from [builtins](builtins.md), normally provided by default startup.

With no argument, if `serverctl` reports a nonempty `005 MAP` token, the alias sends `MAP` through `quote` and returns. That branch uses the server's own output and semantics; it does not collect LINKS or apply the script's renderer.

Otherwise it requests the current server's LINKS list, captures numeric `364` records until `365`, and builds a list of neighboring servers. With an explicit server argument it always takes this client-side path and attempts to use that name as the map's center. The argument is an exact name in the returned LINKS data, compared case-insensitively; it is not a wildcard or a request to connect to another server.

## Output

The local renderer prints one line per visited server, using branches such as `|-` and `` `-``:

```text
[map] <branches><server> (<hops> <description>)
```

It walks outward from the selected center and avoids revisiting names. The displayed hop counts remain the values reported by LINKS from the querying server's perspective; choosing another center does not recalculate those numbers. If the chosen center is not in the returned list, it prints `[map] server <name> does not appear to be online`. That means absent from this reply, not independently verified offline.

The script has no settings, success count, or function return value. Normal `echo` routing determines where the rendered lines appear. There is no saved-window lock or persistent topology cache intended for later queries.

## Server requirements and limits

Both branches require a usable IRC connection. The server controls MAP availability and the data returned by LINKS. It may restrict queries to operators, hide names or links, simplify the topology, rate-limit requests, or reject the request entirely. This script cannot discover information the server withholds; an incomplete visible tree is not proof of the network's complete physical topology.

The client-side path installs suppressing `364` and `365` handlers matching every reply, without restricting them to a server or saving earlier handlers for those patterns. It removes them when `365` arrives, renders the collected data, and deletes its `map.links*` assignments. Existing matching hooks are not restored.

A failed request or missing end marker leaves these temporary handlers installed. Concurrent LINKS queries or map invocations can mix results and callbacks, since the data structures are global rather than per-request or per-server. Use one map query at a time. The renderer assumes conventional server/uplink/hop/description fields and consistent nonnegative hop values; it does not validate malformed topology data or supply a timeout recovery path.

## Examples

```text
/load map
/map
/map irc.example.net
```

The second command selects the server MAP branch when advertised, otherwise builds a local map. The final command builds locally and centers on the named server only if it appears in the returned LINKS list.

## History

The earlier `imap` script by daemon provided client-side topology mapping and arbitrary centering. This `map` implementation was written by wd to emulate the server-side MAP command where unavailable, and first appeared in EPIC4-1.1.6.

## Source references

- [script/map](../../script/map): advertised-MAP branch, LINKS handlers, center selection, rendering, and cleanup.
- [source/numbers.c](../../source/numbers.c): LINKS `364`/`365` processing and numeric-hook fields.
- [source/commands.c](../../source/commands.c): LINKS and quoted protocol requests.
- [source/hook.c](../../source/hook.c): numeric matching, suppression, and handler replacement.
- [script/builtins](../../script/builtins): encoding and server-context wrappers.
