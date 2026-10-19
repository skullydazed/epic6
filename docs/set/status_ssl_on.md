# set status_ssl_on

## Synopsis

```text
/set status_ssl_on [<text>]
/set -status_ssl_on
```

Default: `*SSL*`. With no text, [set](../commands/set.md) displays the current value. Unsetting removes this marker and permits the fallback described below.

## Description

Sets the status-bar text selected when the window has a server whose connection socket uses TLS, historically called SSL. It shares the `%|` field in [status_format](status_format.md) with [status_ssl_off](status_ssl_off.md).

The renderer returns `status_ssl_on` when the TLS condition holds and this setting is defined. Otherwise it returns `status_ssl_off` when defined, or an empty field. Therefore unsetting `status_ssl_on` alone can make a TLS connection display the off marker. An empty but defined on-string instead supplies empty text without taking the fallback. A window with no server uses the off branch.

The text is inserted directly: `%|` inside this setting is not a nested substitution. The outer global or per-window format must contain `%|`. Each window checks its own socket state, without the current-window restriction of [show_status_all](show_status_all.md).

## Effects and limitations

Changing this text rebuilds status formats and requests updates. It does not enable or disable TLS, establish a connection, change certificate validation, or send an IRC request. The selected field reports socket TLS state rather than successful certificate verification; the label itself is user-configurable.

Ordinary command parsing can expand dollar expressions on assignment. If [status_does_expandos](status_does_expandos.md) is enabled, dollar expressions in the assembled status line can additionally expand afterward.

## Example

Customize the TLS connection marker:

```text
/set status_ssl_on (SSL)
```

An outer `%|` supplies `(SSL)` when the on branch applies. Restore the default marker with `/set status_ssl_on *SSL*`. To remove both branches' visible text, unset both `status_ssl_on` and `status_ssl_off`; neither operation changes connection security.

## History

This setting originated in the EPIC4-SSL project and first appeared in EPIC4-1.1.3.

## Source references

- [source/vars.c](../../source/vars.c) and [include/config.h](../../include/config.h): string registration, status rebuild callback, and default marker.
- [source/status.c](../../source/status.c): `%|` mapping and `status_ssl` on-string selection followed by off-string fallback.
- [source/server.c](../../source/server.c): `get_server_ssl_enabled` checks the associated socket's TLS state.
