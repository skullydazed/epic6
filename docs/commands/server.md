# server

## Synopsis

```text
/server
/server <server description or refnum>
/server -help
/server -add <server description>
/server -update <refnum> <server description>
/server -delete <server description or refnum>
```

`server` manages EPIC's server list and changes which server your windows use. It establishes client connections; [connect](connect.md) sends an IRC request asking a server to connect to another server.

## How EPIC gets your server list

At startup, EPIC adds server descriptions from the command line and the whitespace-separated `IRCSERVER` environment variable. If the list is empty, or the command-line `-a` option requests additional servers, it reads the file named by `IRC_SERVERS_FILE`. The current implementation has no built-in fallback filename or default server list. The file path supports normal filename expansion, including a leading tilde; a relative path is relative to the working directory.

The file contains one description per line. Empty lines and lines beginning with `#` are skipped. A line beginning with `[group]` sets the default group for subsequent entries that do not specify their own group.

## Server descriptions and references

The conventional description is:

```text
host:port:password:nickname:group:type:protocol:vhost:certificate
```

Leave positional fields empty to skip them. Alternatively, use named fields to avoid counting colons:

```text
irc.example.net:PORT=6697:TYPE=IRC-SSL:NICK=JoeBob:GROUP=example
```

Once named fields begin, subsequent fields must also be named. The supported fields are:

| Field | Meaning |
| --- | --- |
| `HOST` | DNS hostname or IP address. Surround an IPv6 address with square brackets so its colons remain part of the address. |
| `PORT` | Server port; defaults to EPIC's startup port, normally 6667, configurable with `IRCPORT` or command-line `-p`. |
| `PASS` | IRC registration password, when required by the server. |
| `NICK` | Nickname requested at registration; an unspecified nickname uses EPIC's default nickname. |
| `GROUP` | A label used to identify related server entries. |
| `TYPE` | `IRC` for ordinary IRC, or `IRC-SSL` for TLS. The default is `IRC`. |
| `PROTO` | Address family: `tcp4`, `tcp6`, or `tcp` for either. An unspecified or unrecognized value permits either family. |
| `VHOST` | Local hostname or address to bind for this connection. |
| `CERT` | Local PEM certificate file for TLS client authentication. |

The underlying description parser also accepts JSON objects. For interactive use, a colon-separated description without spaces is the simplest form: the ordinary connection command reads only its first whitespace-delimited argument, and a separate port argument is ignored. The `-add` and `-update` description arguments support double-quoted words.

A server refnum is the number shown by `/server`, beginning at zero. Deleted numbers can be reused. A description's host field can also match a hostname, the server's reported name, group, advertised network name, or alternate name, using wildcard matching. If supplied, a nonzero port and password further restrict the match. Open servers are searched before closed servers; within each set, the lowest matching refnum wins. Use the refnum when you need a particular entry.

## Servers and windows

Several windows can share one server, and EPIC can maintain several server connections. A window has at most one server association, which can refer to a server that is not currently connected.

`/server <reference>` moves **all windows associated with the current server** to the selected server. If the selected server is not registered, EPIC schedules a connection or reconnection; DNS lookup, connection, TLS negotiation, and IRC registration happen asynchronously. Selecting an already registered server reuses its connection. Selecting the current server reports its association and schedules reconnection if needed.

The old server normally closes after it has no windows, with the quit message `Changing servers`; automatic closure can be disabled through the server's autoclose property. To change only one window's association, use [window](window.md)'s `server` operation.

## Options

| Form | Effect |
| --- | --- |
| `/server` | Lists refnums, hosts, ports, groups, connection types and states, virtual hosts, and known nicknames. |
| `/server -help` | Prints usage. |
| `/server -add <description>` | Creates an entry, or merges supplied fields into a matching entry, without moving windows or initiating a connection by itself. |
| `/server -update <refnum> <description>` | Merges supplied fields into an existing entry identified by numeric refnum. Refuses an open server. |
| `/server -delete <reference>` | Removes an existing, closed entry. Refuses to delete the last remaining entry. |
| `/server <reference>` | Selects an existing entry or creates one when no match exists, then moves windows and schedules a connection as needed. |

Option names are case-insensitive and accept nonempty abbreviations such as `-a` and `-d`. Only the expected arguments are used; additional arguments are ignored. The built-in does not implement `+`/`-` server cycling, signed connection/disconnection forms, or a `-safe` option. Use [disconnect](disconnect.md) to close a connection.

An ordinary description that matches an existing entry selects it without merging changed fields. Use `-update` to change its configuration. Unlike `-update`, `-add` does not reject an open matching entry; changing its stored description does not redo the existing connection. Prefer updating a disconnected entry. The `-update` confirmation does not validate that the refnum exists, so check the list afterward when changing entries.

## TLS and restrictions

`TYPE=IRC-SSL` enables TLS. During connection setup, EPIC changes port 6667 to 6697 for that type; conversely, `TYPE=IRC` on port 6697 is changed to `IRC-SSL`. Specify the intended type and port explicitly for other TLS ports. TLS certificate validation and hooks can reject the connection. EPIC's build requires an OpenSSL-compatible TLS library; `configure --with-ssl=PATH` can select its installation.

Adding or selecting an entry does not prove its hostname, port, password, nickname, or certificate is valid. Network failures, certificate policy, and server registration rules determine whether it becomes connected. Missing option arguments and prohibited updates or deletions print diagnostics. This command has no script return value.

## Examples

```text
/server irc.example.net:6668
/server irc.example.net:::JoeBob
/server -add irc.example.net:6667::blah
/server -add irc.example.net:PORT=6697:TYPE=IRC-SSL:GROUP=example
/server 2
/server -update 2 irc.example.net:PORT=6697:TYPE=IRC-SSL
/server -delete 5
/server [2001:db8::1]:PORT=6697:TYPE=IRC-SSL
```

The numeric examples require those entries to exist; updating and deleting require them to be closed. The example addresses are placeholders.

## Source

Registration is in [source/commands.c](../../source/commands.c). Command parsing, description lookup and merging, server-list handling, connection setup, and TLS policy are in [source/server.c](../../source/server.c). Description field parsing is in [source/ircaux.c](../../source/ircaux.c), startup list and port selection in [source/irc.c](../../source/irc.c), and window migration and automatic connection handling in [source/window.c](../../source/window.c). TLS support is in [source/ssl.c](../../source/ssl.c) and its build requirements in [configure.ac](../../configure.ac).
