# stats

## Synopsis

```text
/stats [<type> [<server>]]
```

## Description

`stats` asks an IRC server for statistics or configuration information. Some queries report counts or uptime; others describe connections, access rules, or operator configuration. The server defines which queries exist and who may use them.

The conventional type is a single letter, without a leading hyphen. An optional server argument requests information from that network server; when omitted, the receiving server normally answers. Server names, masks, and remote-query permissions are interpreted by the server, not EPIC.

The built-in forwards `STATS` followed by the complete argument text. With no arguments, it sends bare `STATS`: EPIC does not select a default type or print local usage. The conventional protocol returns an end-of-stats reply for that form, while some implementations provide help or other output.

## Query types

These letters describe conventional or commonly used categories, **not a list guaranteed by EPIC**. Meanings, letter case, availability, and displayed fields can differ between server implementations.

| Type | Typical information |
| --- | --- |
| `l` | Connection durations and traffic counts. |
| `m` | Protocol command usage counts. |
| `o` | Configured operator access. |
| `u` | Server uptime. |
| `c` | Server connection configuration. |
| `h` | Hub or leaf connection rules. |
| `k` | Client bans. |
| `i` | Client access rules. |
| `y` | Connection classes and limits. |
| `p` | On some servers, active operators and idle information. |

Consult your network's documentation for its supported letters and permissions. A query's output need not correspond to a literal line in a configuration file. [mode](mode.md) and [trace](trace.md) are separate requests and do not determine which statistics the server allows.

## Output and restrictions

Replies and errors arrive asynchronously. EPIC uses numeric-specific [on](on.md) handlers, then `on numeric`, before default display. Numeric 219 is the conventional end-of-stats reply; its display includes the server's reply text. The command does not collect results into a local statistics object and has no script return value.

The server can restrict types or fields to operators, limit remote queries, or rate-limit requests. EPIC does not bypass those policies. An empty or restricted reply does not prove that the requested configuration is absent.

Sending requires a usable connection in the current server context, or the primary server when there is no current context. A closed selected connection normally produces a diagnostic through the `disconnect` hook; with no server entry, nothing is sent. Normal outgoing encoding and `on send_to_server` handling apply. The command is always registered and has no client-side options.

## Examples

```text
/stats u
/stats m
/stats u irc.example.net
```

These conventionally request local uptime, local command-use counts, and uptime from a specified network server, where allowed.

## Source

Registration and `send_comm()` are in [source/commands.c](../../source/commands.c); the shared handler has no special `stats` branch. Sending and connection checks are in [source/server.c](../../source/server.c), and numeric hooks and display are in [source/numbers.c](../../source/numbers.c). Conventional parameters and the `l`, `m`, `o`, and `u` queries are described in [RFC 2812, section 3.4.4](https://www.rfc-editor.org/rfc/rfc2812#section-3.4.4).
