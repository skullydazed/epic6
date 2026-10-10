# vhosts command

## Synopsis

```text
/vhosts
/vhosts hostname-or-address
```

## Technical

With no arguments, `vhosts` lists EPIC's cached local addresses that can be used when selecting a connection's local host. At startup, EPIC enumerates IPv4 and IPv6 interface addresses with `getifaddrs()`. Successful later local-host lookups can add entries. Running `vhosts` does not refresh the interface inventory or test that every cached address remains usable.

Each entry is displayed in this format:

```text
Vhost=index, family=ipv4-or-ipv6, hostname=name, paddr=address, sl=length, is_default=flag
```

`Vhost` is a zero-based cache index, `family` identifies IPv4 or IPv6, `hostname` is the stored name, and `paddr` is the numeric address. Startup entries initially use the numeric address for both name and address. `sl` is the socket-address structure length, not a network prefix length. `is_default=1` marks the selected default for that address family; zero means it is not marked as that default. The list can contain loopback addresses or duplicate entries and does not display interface names.

With nonempty arguments, the command takes a different path: it passes the **entire argument string** to the asynchronous resolver as one hostname, allowing IPv4 or IPv6 results. It writes a JSON result directly to standard output, outside normal window output and hooks. Resolution failures can also produce a diagnostic and a JSON `failure` status. This form does not select a local address, change the default, or add the result to the vhost cache. `localhost` is specially resolved as `127.0.0.1` in this path.

## Practical

Use the bare command to inspect cached choices, then use [`hostname`](hostname.md) to select defaults for future connections. A per-server `vhost` in a [`server`](server.md) description can override those defaults. Existing connections are unaffected by selecting another local address, and the host visible to other IRC users remains subject to server lookup and cloaking policies.

The argument form is a resolver diagnostic, not an IRC request for a virtual hostname. Output sent directly to standard output may be awkward to read in fullscreen mode.

## Options and restrictions

There are no options or function return value. Argument text is not split into separate hostnames or unquoted; supply one plain hostname or numeric address. A token such as `-help` is treated as a hostname to resolve.

The command is always registered and requires no IRC connection. Interface enumeration depends on the operating system's `getifaddrs()` results; failure is reported during initialization. The cache holds at most 1024 entries, and additional entries are rejected with a diagnostic when it fills. The resolver form uses EPIC's c-ares resolver and can complete after the command returns. Neither form guarantees that an address is suitable for a particular remote server or remains assigned to your machine.

## Examples

```text
/vhosts
/vhosts 127.0.0.1
```

The first lists cached entries. The second requests resolver JSON for the loopback address without selecting it as a connection default.

## Source

[source/commands.c](../../source/commands.c) registers `vhosts`. [source/network.c](../../source/network.c) implements `vhostscmd()`, interface caching, local-host lookup, `hostname_to_json()`, and resolver JSON output. [source/irc.c](../../source/irc.c) initializes the cache and applies startup host defaults.
