# set accept_invalid_ssl_cert

## Synopsis

```text
/set accept_invalid_ssl_cert [on|off|toggle]
```

Default: `on`. With no value, [set](../commands/set.md) displays the current value.

## Description

Controls the default acceptance policy for server SSL certificates. When on, EPIC accepts verification failures only if all reported problems are self-signed certificates or hostname mismatches. Other verification errors still cause rejection. When off, any verification error causes rejection under this policy. Valid certificates are accepted in either case.

This is evaluated during connection setup; changing it does not recheck existing connections. Scripts can override certificate acceptance through the server SSL hooks, so this setting is not an unconditional final decision. See [on](../commands/on.md) and [ssl_root_certs_location](ssl_root_certs_location.md).

## Example

```text
/set accept_invalid_ssl_cert off
```

## Source references

- [source/server.c](../../source/server.c): setting behavior.
- [source/vars.c](../../source/vars.c) and [include/config.h](../../include/config.h): registration, callback, and default.
