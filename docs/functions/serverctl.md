# $serverctl()

## Synopsis

`$serverctl(refnum <server description>)`

`$serverctl(omatch|imatch|gmatch <pattern>)`

`$serverctl(get <refnum> <property> [argument])`

`$serverctl(set <refnum> <property> [value])`

## Technical

A low-level interface to EPIC's server structures. Operations and property names are case-insensitive and accept prefixes, resolved in dispatch order rather than requiring a unique match. Use full names to avoid ambiguous abbreviations. Refnum `-1` in server-specific operations selects the current execution server, when one exists. Other refnums must identify an existing server entry.

Operations, properties, and lookup descriptions use normal function word parsing, with quoted grouping controlled by [/xdebug dword](../commands/xdebug.md). String setter values and match patterns generally use the entire remaining text: quotes in that text are literal. Numeric setters consume one integer argument; extra words are ignored. Missing required input or unknown operations/properties generally return empty.

| Operation | Result or effect |
| --- | --- |
| `refnum <description>` | Matching refnum, or empty when not found; accepts numeric refs or [/server](../commands/server.md) descriptions |
| `omatch <pattern>` | Refnums whose configured hostname matches |
| `imatch <pattern>` | Refnums whose protocol server name matches |
| `gmatch <pattern>` | Refnums whose group matches |
| `last_server` | Refnum of the last server recorded as sending data |
| `from_server` | Current execution server refnum; [/xeval -s](../commands/xeval.md) can select the context |
| `max` | Server slot bound: iterate from zero while refnum is **less than** this value; deleted slots can be absent |
| `allgroups` | Deduplicated list of server groups |
| `update <refnum> <description>` | Updates an existing entry's description fields; returns refnum or empty if invalid |
| `read_file <path>` | Imports server entries from a file; returns empty even on success |
| `dont_connect [integer]` | Reads, or sets and returns, the startup connection-suppression flag |
| `reset <refnum>` | Clears protocol/channel/nickname state and performs a soft reset; returns `1` for an existing entry |
| `insert`, `delete` | Unimplemented; return empty without adding or deleting entries |

Description lookup prefers an open matching entry, then a closed entry, with ascending refnums within each group. The host pattern can match configured hostname, protocol name, group, advertised `NETWORK`, or alternate names. A supplied nonzero port must match; a supplied password pattern must also match. Lookup is not restricted to the description's first two fields.

`read_file` expands tilde prefixes and resolves the pathname, then reads the server-list format with `#` comments and `[group]` headings. Import failures are not reflected in its return value. `reset` destroys cached channels and registration details while retaining an existing socket; it does not send a QUIT or perform a normal reconnect. It is intended for protocol-session resets such as bouncer replay.

## Properties

“Both” means `get` and `set` are implemented. Setters often modify local state rather than sending a protocol command. Runtime information can be empty, stale, or unavailable when disconnected.

| Property | Access | Meaning and setter effect |
| --- | --- | --- |
| `name`, `port` | Both | Configured connection hostname and port; changes apply to later connections |
| `ssl`, `protocol` | Get | Configured type, normally `IRC` or `IRC-SSL`; not proof of negotiated TLS |
| `ssl`, `tls` | Set | Stores protocol type text; use `IRC-SSL` to request TLS, not integer `1`; applies to later connections |
| `vhost`, `cert` | Both | Connection bind host and TLS client-certificate setting for later connections |
| `password` | Both | Registration password for later connections |
| `nickname` | Both | Get confirmed nickname; set default/pending nickname and send NICK if the socket is open |
| `realname` | Both | Get current registration realname; set changes the default for later registration |
| `default_realname` | Both | Default realname for later registration |
| `away` | Both | Stored away message; set sends AWAY when registered, or stores it for registration; empty clears it |
| `away_status` | Both | Cached actual away-status integer; setting changes local state only |
| `umode` | Both | Cached user modes; setting is allowed only while the server is not open; use [/mode](../commands/mode.md) for a live change |
| `group` | Both | User-defined server group |
| `altname`, `altnames` | Both | Get all alternate names; set `altname` adds one whole value, set `altnames` replaces the list using double-quoted word parsing |
| `autoclose` | Both | Normally `1`; zero prevents automatic closure merely because no windows use the server |
| `quit_message` | Both | Message used when leaving the server |
| `maxcachesize` | Both | Channel-size threshold for initial userhost WHO population; zero skips nonempty channels, negative values remove the size threshold |
| `isonlen` | Both | ISON request length budget in bytes, not number of nicknames; this setter does not enforce a minimum |
| `maxison`, `maxuserhost` | Both | Limits concurrent dispatched requests; zero removes the limit, rather than rejecting queued commands |
| `itsname`, `cookie`, `unique_id`, `userhost`, `version` | Both | Locally stored protocol server name, reconnection cookie, identifier, userhost, and version; setters do not send corresponding requests |
| `005 <token>` | Both | Cached ISUPPORT token value; empty setter value stores an empty value, retaining the token |
| `005s [pattern]` | Get | Cached ISUPPORT token names, optionally wildcard-filtered |
| `connected` | Get | Whether an IRC protocol session is registered |
| `open` | Get | Whether a server socket is open; distinct from registered |
| `state`, `status` | Get | Operational state name; `status` is an alias |
| `addrfamily` | Get | Remote address family: `ipv4`, `ipv6`, or `unknown` |
| `paddr`, `localport` | Get | Remote presentation address and local socket port |
| `addrsleft` | Get | Number of untried addresses from the current resolution |
| `next_server_in_group` | Get | Next server refnum in the same group |
| `fulldesc` | Get | Unimplemented placeholder: returns `XXX TODO XXX` |
| `primary` | Set | Changes the primary server refnum |
| `cap_hold` | Set | Holds CAP negotiation while nonzero; clearing permits the CAP handler to continue to CAP END |

Setting `005 CASEMAPPING` also changes local nickname/channel comparison behavior, and setting any `005` token requests status refreshes.

State names are `CREATED`, `RECONNECT`, `POLICY`, `DNS`, `CONNECTING`, `SSL_CONNECTING`, `REGISTERING`, `SYNCING`, `ACTIVE`, `EOF`, `ERROR`, `CLOSING`, `CLOSED`, and `DELETED`.

### TLS properties

The following getters require TLS to be enabled for the server; otherwise they return empty. They describe the current session and certificate, not a general certificate-validation service.

| Property | Result |
| --- | --- |
| `ssl_cipher`, `ssl_version` | Negotiated cipher and TLS version |
| `ssl_pem`, `ssl_cert_hash`, `ssl_pkey_bits` | Certificate PEM, hash, and public-key bit count |
| `ssl_subject`, `ssl_issuer` | Certificate subject and issuer |
| `ssl_subject_url`, `ssl_issuer_url` | URL-encoded subject and issuer |
| `ssl_sans` | Certificate subject alternate names |
| `ssl_verify_error`, `ssl_checkhost_error`, `ssl_self_signed_error`, `ssl_other_error` | Error indicators, normally zero or one after verification; some unavailable results use `-1` |
| `ssl_most_serious_error` | Stored OpenSSL verification error code |
| `ssl_accept_cert` | Stored certificate-acceptance decision; also settable |
| `ssl_verify_result`, `ssl_checkhost_result`, `ssl_self_signed` | Unimplemented; return empty |

## Returns

Getters return the selected string, integer, or list. Empty can mean unavailable data, invalid refnum/property, or a legitimate empty value.

Most setters return `1`. Setters for `ssl`, `tls`, `unique_id`, `userhost`, `version`, `vhost`, `altname`, `altnames`, `realname`, and `default_realname` return empty even after changing state. `set ... 005` returns `1` for a nonempty value and `0` when storing an empty value. `umode` returns empty while open. An unknown `ssl_...` setter can return `1` without changing anything when TLS is enabled. A setter result does not confirm server acceptance of a request.

## Examples

| Expression | Meaning |
| --- | --- |
| `$serverctl(refnum irc.example.org)` | Refnum matching that server description, or empty |
| `$serverctl(get 2 connected)` | Registration status of server 2, or empty if nonexistent |
| `$serverctl(gmatch EFnet)` | Refnums in matching server groups |
| `$serverctl(set 1 ssl IRC-SSL)` | Configure TLS for server 1's next connection; returns empty |
| `$serverctl(get -1 005 CHANTYPES)` | Current execution server's cached channel prefixes |

Implementation, dispatch, lookup, setters, and state table: [source/server.c](../../source/server.c). Registration and wrapper: [source/functions.c](../../source/functions.c). TLS metadata: [source/ssl.c](../../source/ssl.c). Queue handling: [source/who.c](../../source/who.c). CAP negotiation: [source/parse.c](../../source/parse.c).
