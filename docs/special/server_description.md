# Server descriptions

A server description supplies configuration for an IRC server entry or identifies an existing entry. [Server](../commands/server.md) can add, update, and connect entries; other interfaces may only look them up. A description does not universally create a connection wherever a “server” argument is accepted. See [referring to servers](referring_to_servers.md).

## About server descriptions

The familiar form is a colon-separated sequence:

```text
host:port:password:nickname:group:type:protocol:vhost:certificate
```

The current positional fields and named equivalents are:

| Position | Name | Meaning and fallback |
| --- | --- | --- |
| 1 | `host` | DNS hostname or address literal. Required to describe a new destination. |
| 2 | `port` | Destination port; an omitted/empty value uses the client's default IRC port, initially `6667` and adjustable at startup. |
| 3 | `pass` | IRC registration password; omitted/empty means no `pass` request. This is not automatically account authentication. |
| 4 | `nick` | Nickname requested at registration; otherwise use the entry's current/default nickname, initially derived from the client's startup nickname. |
| 5 | `group` | Local group label. The getter represents an omitted/empty group as `<default>`. |
| 6 | `type` | `irc` or `irc-ssl`; omitted/empty uses `irc`. |
| 7 | `proto` | Address family: `tcp4`, `tcp6`, or `any`, with documented aliases described under [IPv6](ipv6.md). Omitted/empty permits either family. |
| 8 | `vhost` | Requested local source address/hostname; otherwise use a family-specific default or operating-system choice. |
| 9 | `cert` | Local client certificate file for TLS, when supplied. |

Fields not needed can be omitted at the end or skipped with empty positional fields. Omitting a field while updating an existing entry is not necessarily the same as clearing it: descriptions can be merged into stored configuration. Use [server](../commands/server.md) and [serverctl](../functions/serverctl.md) for the specific update interface.

IPv6 literals contain colons, so bracket them within the field:

```text
[2001:db8::1234]:PORT=6697:TYPE=IRC-SSL:PROTO=tcp6
```

The address is illustrative, not a working server. The brackets protect the field's contents from colon splitting. Bracketed values can also protect other delimiter-containing fields; backslash escapes are removed by the field parser. This syntax is distinct from a URL.

## Skipping fields with names

Named fields avoid counting empty positions:

```text
irc.example.net:PORT=7002:TYPE=IRC-SSL
irc.example.net:GROUP=worknet:TYPE=IRC-SSL:PROTO=tcp6
```

Replace example hosts with actual network endpoints. Names are case-insensitive, but use the full names listed in the table: `proto`, not an assumed abbreviation such as `pr`, and `nick`, not `nickname`.

You may begin with positional fields and then switch to named fields. **After the first named field, every following field must also have a name.** An unnamed field after that point is warned about and ignored. Thus:

```text
irc.example.net:GROUP=worknet:IRC-SSL
```

sets the group but does not set the type. Write `TYPE=IRC-SSL` instead. Named fields can appear in any order once named syntax begins.

Avoid repeating a key. The colon parser adds duplicate object members rather than implementing a reliable last-value-wins rule, and ordinary field lookup finds the first matching member. Unknown keys are not automatically rejected or expanded into known field names; storing one does not make the connection code use it.

## Connection type and defaults

`TYPE=IRC-SSL` selects TLS independently of IPv4 versus IPv6. The current connection code corrects two common port/type combinations: an `irc-ssl` entry using port `6667` is changed to `6697`, and an `irc` entry using port `6697` is changed to `irc-ssl`. Specify both fields explicitly when the network uses a nonstandard port.

A requested vhost must resolve to an address usable for a local bind in the destination's address family. If it cannot be used, current selection can fall back to defaults rather than guaranteeing that source. A password, nickname, or client certificate request is also subject to server-side policy; successful parsing does not mean registration or authentication succeeds.

The parser also accepts a JSON object as a configuration document in interfaces that pass it intact. Surrounding command tokenization and JSON value types matter; the colon-separated form above is the straightforward form for ordinary interactive use. See the caller's documentation before substituting JSON into another interface.

## Server refnums and the server list

An existing entry has a nonnegative refnum starting at `0`. A numeric reference can select that entry directly. New insertion uses a vacant slot or extends the list; deletion does not renumber other entries, but the vacant number can later be reused.

Do not assume hostname and port are the only identity key. Shared lookup can match host patterns against the configured host, server-reported name, local group, advertised network, or alternate names, with specified port/password constraints. Open entries are searched before closed ones and the lowest refnum in the applicable search wins. A caller performing an upsert may therefore update an existing match instead of adding a new entry. See [referring to servers](referring_to_servers.md) before using broad patterns for updates.

## Examples

```irc
/server -add irc.example.net:PORT=6666:NICK=JoeBob
/server irc.example.net:PORT=8000:TYPE=IRC-SSL
```

The first adds or updates configuration without requesting a connection. The second selects or creates the described entry and requests a connection using TLS on port 8000. The network must actually provide that endpoint. Replies, certificate acceptance, and registration errors follow the normal connection path; there is no function return value from the command.

## Source references

- `source/server.c`: positional field table, configuration parsing, lookup/upsert, getters, registration, port/type corrections, and connection setup.
- `source/ircaux.c`: `get_kwarg_field()` and positional-to-named transition rules.
- `source/cJSON.c`: duplicate-member insertion and first-match field lookup.
- `source/network.c`: address families and local source selection.
- `source/irc.c`: startup port and nickname defaults.
