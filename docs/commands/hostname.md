# hostname

Select default local addresses for future IRC server connections.

## Synopsis

```text
/hostname
/hostname hostname-or-address
/hostname ipv4-hostname-or-address/ipv6-hostname-or-address
/hostname ipv4-hostname-or-address/
/hostname /ipv6-hostname-or-address
/hostname -
```

## Description

Every network connection has a local IP address. Ordinarily the operating system chooses it. If your machine has multiple addresses, `hostname` lets you select one of them as EPIC's default for subsequent server connections. It does not change your machine's hostname or send a request to rename your host on IRC.

With no arguments, the command displays the stored IPv4 and IPv6 names, showing `<default>` for an unset name. A single hostname is tried separately for both address families. A slash separates distinct IPv4 and IPv6 choices; omitting one side leaves that family's setting unchanged. Each family is updated independently, so one can succeed while the other fails. The command prints a summary of the changes.

Supply a resolvable hostname or numeric IP address that your machine can bind locally. EPIC resolves each requested family and tests returned addresses by creating and binding a socket. The first usable address is cached. A failed lookup or bind leaves the previous setting for that family unchanged; socket and bind failures can produce additional diagnostics. Resolution is synchronous and can delay the client while the resolver runs. IPv6 selection requires IPv6 support and a usable configured IPv6 address.

An explicit usable per-server `vhost` takes precedence over these defaults. Otherwise a new server connection tries the default for its address family. Existing connections are unaffected: reconnect before expecting a new local address to be used. The name other users see remains subject to the server's reverse lookup, cloaking, and other policies.

`/hostname -` clears both stored names. **Current reset limitation:** it does not clear default flags on cached addresses. A previously selected cached default can consequently still be used for a later connection even though the display shows `<default>`. Do not rely on this command to restore operating-system address selection after setting a default; restarting without a configured default avoids that retained cache state.

## Examples

```text
/hostname
/hostname local.example.net
/hostname 192.0.2.10/2001:db8::10
/hostname /2001:db8::20
/hostname -
```

The numeric addresses above illustrate syntax; replace them with addresses actually configured on your machine. The fourth example changes only the IPv6 choice.

## Restrictions and source

Use the address or slash-separated pair as the whole argument; there are no command options. Address availability can change after successful validation, and cached entries are reused rather than resolved and bind-tested again. The command is always registered.

Registration and `e_hostname` are in [source/commands.c](../../source/commands.c). Family-specific validation, caching, default selection, and summaries are in [source/network.c](../../source/network.c). New server connections use these defaults in [source/server.c](../../source/server.c), and startup default-hostname configuration is in [source/irc.c](../../source/irc.c).
