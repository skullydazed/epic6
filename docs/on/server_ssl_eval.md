# on server_ssl_eval

## Synopsis

```text
/on [<noise>]server_ssl_eval "<pattern>" [(<argument list>)] { <commands> }
```

## Description

Triggered during TLS setup when EPIC evaluates a server certificate using its default policy, after [on ssl_server_cert](ssl_server_cert.md).

Use standard [on](../commands/on.md) matching, serial numbers, and noise controls. The pattern matches the complete event data.

## Parameters

| Parameter | Meaning |
| --- | --- |
| `$0` | Server refnum. |
| `$1` | Configured server name. |
| `$2` | Certificate verification error indicator. |
| `$3` | Hostname-check error indicator. |
| `$4` | Self-signed certificate error indicator. |
| `$5` | Other certificate error indicator. |
| `$6` | Proposed acceptance value: `1` to accept, `0` to reject. |

## Behavior

If the earlier certificate hook has already assigned `SSL_ACCEPT_CERT`, this event is skipped. Otherwise EPIC accepts certificates without verification errors. With errors, [set accept_invalid_ssl_cert](../set/accept_invalid_ssl_cert.md) permits acceptance only when there are no “other” errors. EPIC then invokes this hook with its proposal.

The hook’s suppression result is ignored. To change the decision, use [serverctl](../functions/serverctl.md): `SET <server> SSL_ACCEPT_CERT 0` rejects and `SET <server> SSL_ACCEPT_CERT 1` accepts. EPIC checks the stored value after the hook and aborts the connection if it is zero. The hook itself has no default display.

## Example

```text
on -server_ssl_eval "*" {
    echo TLS server $0 ($1): verify=$2 host=$3 self-signed=$4 other=$5 accept=$6;
};
```

## Source references

- [source/server.c](../../source/server.c): `server_io` computes policy, invokes the event, and enforces acceptance; `serverctl` implements `SSL_ACCEPT_CERT`.
- [source/hook.c](../../source/hook.c): event registration and standard hook dispatch.
