# IPv6 support

EPIC6 supports IPv6 server addresses and connections, IPv6 local source addresses, and reverse lookup of IPv6 address literals. Availability still depends on operating-system networking, usable local addresses and routes, DNS results, and the destination server.

## Server descriptions

Surround an IPv6 literal with square brackets in a colon-separated server description so its internal colons are not treated as field separators:

```irc
/server [2001:db8::1234]:PORT=6697:TYPE=IRC-SSL:PROTO=tcp6
```

The address above is an example placeholder; replace it with an actual server address. The brackets belong to the server-description syntax, not the address itself. Named fields can be used after the initial hostname, but once named fields begin, subsequent fields must also be named. See [server](../commands/server.md) for the complete syntax.

A hostname can be used instead of a literal:

```irc
/server irc.example.net:PORT=6697:TYPE=IRC-SSL:PROTO=tcp6
```

Replace that example hostname with the network's real server. `TYPE=IRC-SSL` requests TLS; choosing IPv6 alone does not enable TLS.

## Server connections

The `PROTO` field selects which address family the resolver may return:

| Value | Address family |
| --- | --- |
| `tcp6`, `6`, `ipv6`, `v6`, `ip6` | IPv6 only |
| `tcp4`, `4`, `ipv4`, `v4`, `ip4` | IPv4 only |
| Empty, `0`, `any`, `ip`, `tcp` | Either family |

Unrecognized protocol values also currently select either family; use a documented value to make the intent clear. Selection is case-insensitive. With no family restriction, EPIC tries the returned address list rather than promising an IPv6 connection. With IPv6 selected, an IPv4-only server or hostname does not provide a usable IPv6 destination.

EPIC resolves the destination and attempts connections using the resulting addresses. DNS, socket creation, local binding, routing, TLS negotiation, and IRC registration can each fail independently; selecting `tcp6` does not guarantee successful registration. The protocol selection is part of the server configuration, not a separate IPv6 enable/disable build option.

## Address functions

[`iptoname(address)`](../functions/iptoname.md) accepts IPv4 or IPv6 presentation addresses and requests reverse DNS. Pass the IPv6 literal without the server-description brackets:

```irc
@ hostname = iptoname(2001:db8::1234)
```

This illustrative address need not have a reverse-DNS record. The function returns a hostname on success and an empty string on failure, with possible diagnostics. A numeric address is not automatically returned as a substitute when the reverse lookup fails.

Do not assume every address helper supports both families: `nametoip()` currently requests IPv4 in its helper. Consult each function's documentation rather than inferring IPv6 support from its name.

## Virtual hostnames

A virtual host here means the local source address used for a connection. It must be an address the local operating system can bind, not merely an arbitrary public hostname. EPIC chooses an address matching the remote socket's family: IPv6 connections need an IPv6 source address.

A server description's `VHOST` field can request a source hostname or literal. Bracket an IPv6 value within that field, for example `VHOST=[2001:db8::10]`. The address is illustrative and must be replaced by one usable on your host. Resolution and a trial local bind are used to validate uncached choices.

If the requested source cannot be used, the current selection code can fall back to a configured default for that address family, then to an operating-system-selected address. A requested vhost is therefore not a guarantee of the eventual source address. See [hostname](../commands/hostname.md), [vhosts](../commands/vhosts.md), and [server](../commands/server.md) for the related controls.

## Source references

- `source/server.c`: server-description fields, `server_grab_address()`, and connection-address iteration.
- `source/ircaux.c`: bracketed fields in `get_kwarg_field()`.
- `source/network.c`: IPv6 socket/address handling, reverse lookup, `lookup_vhost()`, and `get_default_vhost()`.
- `source/functions.c`: `iptoname()` and `nametoip()` wrappers.
