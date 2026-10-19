# set status_ssl_off

## Synopsis

```text
/set status_ssl_off [<text>]
/set -status_ssl_off
```

Default: `*RAW*`. With no text, [set](../commands/set.md) displays the current value. Unsetting removes this fallback marker rather than restoring the default.

## Description

Sets the status-bar text normally displayed when the window's server connection is not using TLS, historically called SSL. It supplies the fallback text for `%|` in [status_format](status_format.md), or a per-window format.

The field first checks whether the window has a server whose socket uses TLS and [status_ssl_on](status_ssl_on.md) is set. If so, it returns that setting. Otherwise it returns `status_ssl_off` when set, or an empty field. Consequently this marker also appears for a window with no server, and can appear for a TLS connection if `status_ssl_on` is unset. An empty but defined `status_ssl_on` returns empty text instead of this fallback.

The text is inserted directly; `%|` inside this setting is not a nested substitution. The outer format must contain `%|`. Each window checks its own connection, without a current-window restriction from [show_status_all](show_status_all.md).

## Effects and limitations

Changing the text rebuilds status formats and requests updates. It does not enable or disable TLS, connect or disconnect, change certificate validation, or send an IRC request. This field selects presentation from the socket's TLS state; it is not a certificate-verification report. Custom labels and fallback choices can obscure that distinction.

Ordinary command parsing can expand dollar expressions on assignment. If [status_does_expandos](status_does_expandos.md) is enabled, dollar expressions in the assembled status line can additionally expand afterward.

## Example

```text
/set status_ssl_off [-ssl]
```

An outer `%|` displays `[-ssl]` when the fallback branch applies. Hide fallback text with `/set -status_ssl_off`; this does not change connection security or the `status_ssl_on` marker.

## History

This setting originated in the EPIC4-SSL project and first appeared in EPIC4-1.1.3.

## Source references

- [source/vars.c](../../source/vars.c) and [include/config.h](../../include/config.h): string registration, status rebuild callback, and default text.
- [source/status.c](../../source/status.c): `%|` mapping and `status_ssl` TLS/on-text test followed by off-text fallback.
- [source/server.c](../../source/server.c): `get_server_ssl_enabled` checks the server socket's TLS state.
