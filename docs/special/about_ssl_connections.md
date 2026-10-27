# SSL connections to IRC servers

This document describes how EPIC6 establishes a TLS connection to an IRC server, collects certificate verification results, and offers scripts a chance to choose the certificate policy. The original description covered EPIC5 2.1.6; the behavior below has been checked against the current EPIC6 source.

## Step one: Connect to a TLS server

Request a TLS connection with a server description such as:

```irc
/SERVER irc.example.com:6697:type=irc-ssl
```

Replace the example hostname with the server you intend to use. EPIC first establishes the TCP connection, then starts a nonblocking `SSL_connect()` handshake. Selecting port 6697 alone does not request TLS; the `IRC-SSL` connection type does. See [server](../commands/server.md) for server-description syntax.

## Step two: Complete the handshake

A successful TLS handshake establishes encryption, but EPIC still needs to apply its certificate policy before IRC registration. A failed handshake or missing peer certificate prevents this path from proceeding.

## Step three: Collect certificate verification results

OpenSSL calls EPIC's verification callback when it finds a certificate error or finishes checking a certificate without further errors. EPIC records the errors and always returns success from this callback so verification can continue and collect additional errors. The callback gathers information; the server connection code makes the policy decision afterward.

EPIC records three error categories:

- A self-signed server certificate: `X509_V_ERR_DEPTH_ZERO_SELF_SIGNED_CERT` (18).
- A hostname mismatch: `X509_V_ERR_HOSTNAME_MISMATCH` (62).
- Any other verification error.

Only the specific depth-zero self-signed error belongs to the first category; other chain errors belong to the third category. EPIC chooses a representative `most_serious_error` with other errors taking precedence over hostname mismatch, and hostname mismatch taking precedence over the depth-zero self-signed error. With no error, this value is `X509_V_OK` (0).

## Step four: Offer the SSL_SERVER_CERT hook

Before calling `ssl_connected()`, the server connection code initializes its `SSL_ACCEPT_CERT` field to `-1`, meaning that no script decision has been recorded. After collecting certificate metadata, EPIC invokes [`on ssl_server_cert`](../on/ssl_server_cert.md).

| Argument | Meaning |
| --- | --- |
| `$0` | The connection's virtual file descriptor; use `$serverctl(FROM_SERVER)` for the server reference number in this hook. |
| `$1` | Certificate subject, URL-encoded. |
| `$2` | Certificate issuer, URL-encoded. |
| `$3` | Public key length in bits. |
| `$4` | Representative OpenSSL error code: 0 for no error, 18 for the depth-zero self-signed error, 62 for hostname mismatch, or another verification error code. |
| `$5` | Negotiated TLS protocol version, such as `TLSv1.2` or `TLSv1.3`. |
| `$6` | Certificate digest, currently SHA-1 as lowercase hexadecimal bytes separated by colons. |
| `$7` | Hostname mismatch flag: 0 for no error, 1 for an error. |
| `$8` | Depth-zero self-signed flag: 0 for no error, 1 for an error. |
| `$9` | Other verification error flag: 0 for no error, 1 for an error. |
| `$10` | Any verification error flag: 0 for no errors, 1 for one or more errors. |

A script can change the field using `$serverctl(SET <refnum> SSL_ACCEPT_CERT <value>)`. In the current implementation, any value other than `-1` set during this early hook skips both the provisional policy and `SERVER_SSL_EVAL`.

**Current implementation caveat:** the rejection check is inside the skipped policy block. Setting `SSL_ACCEPT_CERT` to `0` in `SSL_SERVER_CERT` therefore does not reject the connection; it also proceeds to IRC registration. To reject through the normal certificate policy path, leave the field at `-1` in this early hook and make the decision in `SERVER_SSL_EVAL` instead. This differs from the original document's claim that an early value of either 0 or 1 is enforced as the final decision.

## Step five: Make a provisional decision

If the early hook left `SSL_ACCEPT_CERT` at `-1`, EPIC chooses a provisional value:

| Verification result | Provisional decision |
| --- | --- |
| No errors | Accept (1). |
| Only depth-zero self-signed and/or hostname mismatch errors, with `ACCEPT_INVALID_SSL_CERT` on | Accept (1). |
| Only those errors, with `ACCEPT_INVALID_SSL_CERT` off | Reject (0). |
| Any other verification error | Reject (0). |

EPIC stores this result in `SSL_ACCEPT_CERT`. The compiled default for [`accept_invalid_ssl_cert`](../set/accept_invalid_ssl_cert.md) is on; a user's settings may change it. That setting excuses only the two specific categories above, not all invalid certificates.

## Step six: Offer the SERVER_SSL_EVAL hook

After making its provisional decision, EPIC invokes [`on server_ssl_eval`](../on/server_ssl_eval.md). Scripts can inspect the metadata and change the decision before it is enforced.

| Argument | Meaning |
| --- | --- |
| `$0` | Server reference number. |
| `$1` | Configured server hostname, as returned by `get_server_name()`. |
| `$2` | Any verification error: 0 for no errors, 1 for an error. |
| `$3` | Hostname mismatch: 0 for no error, 1 for an error. |
| `$4` | Depth-zero self-signed error: 0 for no error, 1 for an error. |
| `$5` | Other verification error: 0 for no error, 1 for an error. |
| `$6` | EPIC's provisional decision: 0 to reject, 1 to accept. |

Use `$serverctl(GET <refnum> <item>)` to read certificate and connection metadata:

| Item | Meaning |
| --- | --- |
| `SSL_CIPHER` | Negotiated cipher name. |
| `SSL_PEM` | Peer certificate in PEM format. |
| `SSL_CERT_HASH` | Certificate digest, currently SHA-1 with colon-separated hexadecimal bytes. |
| `SSL_PKEY_BITS` | Public key length in bits. |
| `SSL_SUBJECT` | Certificate subject. |
| `SSL_SUBJECT_URL` | URL-encoded certificate subject. |
| `SSL_ISSUER` | Certificate issuer. |
| `SSL_ISSUER_URL` | URL-encoded certificate issuer. |
| `SSL_VERSION` | Negotiated TLS protocol version. |
| `SSL_SANS` | Subject alternative names from the certificate. |
| `SSL_CHECKHOST_ERROR` | Hostname mismatch flag: 0 or 1. |
| `SSL_SELF_SIGNED_ERROR` | Depth-zero self-signed flag: 0 or 1. |
| `SSL_OTHER_ERROR` | Other verification error flag: 0 or 1. |
| `SSL_MOST_SERIOUS_ERROR` | Representative OpenSSL verification error code, or 0 for no error. |
| `SSL_VERIFY_ERROR` | Any verification error flag: 0 or 1. |
| `SSL_ACCEPT_CERT` | Current decision field: initially -1 in the early hook, then normally 0 or 1. |

These getters require an existing TLS-enabled server connection; they return an empty string when that prerequisite is absent. See [`serverctl`](../functions/serverctl.md).

To reject the certificate during `SERVER_SSL_EVAL`, evaluate:

```irc
$serverctl(SET <refnum> SSL_ACCEPT_CERT 0)
```

To accept it, evaluate:

```irc
$serverctl(SET <refnum> SSL_ACCEPT_CERT 1)
```

Replace `<refnum>` with the server reference number, available as `$0` in this hook. These are function expressions to use in script context, not standalone IRC commands. Leaving the field unchanged retains EPIC's provisional decision.

## Step seven: Continue or reject

After `SERVER_SSL_EVAL` returns, EPIC rejects the connection when `SSL_ACCEPT_CERT` is exactly 0. Otherwise, it returns to the connection path and starts IRC registration. Scripts should use the documented 0 and 1 values for decisions. The early-hook bypass described in step four is an exception to enforcement at this stage.

## History

The original DokuWiki document described EPIC5 2.1.6. This Markdown migration updates the explanation to match the current EPIC6 implementation, including its early-hook rejection behavior.

## Source references

- [source/server.c](../../source/server.c): `server_io()` handles the handshake result, certificate policy, both hooks, rejection, and IRC registration; `serverctl()` implements the metadata interface.
- [source/ssl.c](../../source/ssl.c): `ssl_startup()`, `ssl_connect()`, `verify_callback()`, and `ssl_connected()` implement negotiation, verification collection, metadata, and `SSL_SERVER_CERT` arguments.
- [source/vars.c](../../source/vars.c) and [include/config.h](../../include/config.h): registration and compiled default of `ACCEPT_INVALID_SSL_CERT`.
