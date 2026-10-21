# Referring to servers

A server connection or configured server entry has several identifiers. Many commands and functions resolve a supplied server description to one local **server refnum**.

| Identifier | Meaning |
| --- | --- |
| Refnum | A nonnegative integer, starting at `0`, identifying a server-list entry. |
| Our name | The host supplied in its server description. |
| Its name | The server's own logical name reported on IRC, which may differ from its DNS host. |
| Group | A local group label from the server description. |
| Network | The network name advertised through the server's `005 NETWORK` field, when available. |
| Alternate names | Additional names associated with that server entry. |

Use [server](../commands/server.md) with no argument to inspect the configured server list. Refnums are local identifiers, not IRC server names or network-wide numbers. Existing entries are not renumbered when another entry is deleted, but a vacant refnum can be reused for a later entry. Do not treat a saved number as a permanent identity across deletion or client restarts.

## Numbers and names

A numeric reference selects the corresponding existing server entry. For an ordinary name or pattern, the shared lookup searches open servers from lowest refnum upward, then closed servers from lowest refnum upward. The first qualifying match wins.

The host part of the query is wildcard-matched against any of the name fields above. Matching a configured group or alternate name is as valid as matching a hostname; hostname matches do not receive a global priority over group matches. For example, an open server `0` whose group matches can beat open server `1` whose hostname matches.

An open match also wins over a lower-numbered closed match because open entries are searched first. “Open” describes the connection state used by the lookup, not a guarantee that IRC registration is complete. Server-reported names and network information may be unavailable before the server has supplied them.

The lookup returns one refnum, not every matching connection. [Patterns](patterns.md) are accepted, but `*.example.net` does not mean “operate on all matching servers.”

## Qualifying a description

A server description can narrow a name match:

```text
irc.example.net:PORT=6697
```

A specified nonzero port must match. If a password field is supplied, the candidate must have a password and it must match that field's wildcard pattern. These constraints apply before matching names. Not every configuration field is a lookup constraint: supplying a group, type, nickname, or protocol field does not make all of them independent mandatory filters in this matcher.

Use the host field to match a group label, for example `worknet:PORT=6697`, rather than assuming `GROUP=worknet` alone identifies an entry. See [server](../commands/server.md) for colon-separated named fields and [IPv6](ipv6.md) for bracketed address literals.

## Checking a reference

The built-in [serverctl](../functions/serverctl.md) lookup is useful in scripts:

```irc
@ selected = serverctl(refnum *.example.net)
```

This returns the selected refnum, or an empty string when nothing matches. Refnum `0` is valid, so do not test the result only for Boolean truth. The lookup itself does not connect, disconnect, or query an IRC server.

Do not use `/disconnect *.example.net` as a general lookup example: the current `disconnect` argument parser consumes ordinary non-dash arguments without selecting that server and can disconnect the current window's server instead. Use its documented no-argument form in the intended server's window; see [disconnect](../commands/disconnect.md).

## Caller-specific behavior

Not every interface accepts every form. Some require a numeric refnum; some interpret omitted or negative values using their current context. Use the documented syntax of the particular command or function rather than transferring special values between interfaces.

If no entry matches, a lookup-only operation fails. Other callers, such as [server](../commands/server.md), can add a new server description and then act on the new entry. A failed lookup does not universally imply creation, and supplying a new hostname to a lookup-only function does not itself establish a connection.

The resolution rules are client-side. Whether a selected connection is usable, a requested action is permitted, or a new connection succeeds depends on the operation and the server. Loaded scripts can also wrap commands and apply their own selection policy.

## Source references

- `source/server.c`: `serverinfo_load()`, `serverinfo_lookup()`, `serverinfo_matches_servref()`, insertion, and `serverctl refnum`.
- `source/commands.c`: callers such as explicit-server evaluation and message targeting.
- `source/reg.c`: wildcard name matching.
