# set ssl_root_certs_location

## Synopsis

```text
/set ssl_root_certs_location [<text>]
/set -ssl_root_certs_location
```

Default: unset. With no value, [set](../commands/set.md) displays the current value. The hyphen form clears the string.

## Description

Selects a location for trusted certificates used in SSL verification. An existing directory replaces the certificate-directory location; an existing regular file replaces the certificate-file location. These are maintained separately, so setting one does not clear the other. The string is a single path, not a list.

Unsetting clears both selected locations and discovers defaults from OpenSSL’s certificate environment variables or compiled locations, accepting a directory or regular file only when it exists. An invalid path leaves the previous selections in place even though the stored setting changes.

The callback loads the selected locations into the shared SSL context if it already exists; otherwise context initialization loads them later. It does not reverify established connections or explicitly remove certificates already loaded into the context. See [accept_invalid_ssl_cert](accept_invalid_ssl_cert.md) for the default acceptance policy.

## Example

```text
/set ssl_root_certs_location /path/to/ca-bundle.pem
/set -ssl_root_certs_location
```

## Source references

- [source/ssl.c](../../source/ssl.c): setting behavior.
- [source/vars.c](../../source/vars.c) and [include/config.h](../../include/config.h): registration, callback, and default.
