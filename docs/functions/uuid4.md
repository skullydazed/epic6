# uuid4

Generate a random version-4 UUID string.

## Synopsis

```irc
$uuid4()
$uuid4(nodashes)
```

## Technical

The default result uses lowercase hexadecimal digits and the form `xxxxxxxx-xxxx-4xxx-yxxx-xxxxxxxxxxxx`, where `y` is `8`, `9`, `a`, or `b`. This fixes the version and variant fields and fills the other positions from random bytes.

The entire argument text must equal `nodashes`, case-insensitively, to omit the hyphens. It is not parsed as a word or option list: quotes, extra text, or surrounding whitespace that reaches the handler prevent that match and select the default form. All other argument text is silently ignored.

Each call obtains 16 bytes from the crypto library's `RAND_bytes` and constructs a fresh string. It does not use the scripting random-number settings or seed. The built-in is always registered; the current build requires an OpenSSL-compatible crypto library rather than a separate UUID library.

## Practical

Use UUIDs as labels for independently created objects. Random generation does not check previous results or guarantee that no collision can occur. Calling the function twice generates two identifiers; the compact form is not a conversion of an earlier result.

## Returns

A 36-character UUID with hyphens, or a 32-character UUID for `nodashes`.

## Restrictions

The implementation ignores the return status of `RAND_bytes`. If random generation fails, it can still format uninitialized bytes into a UUID-shaped string without returning an error. A correctly shaped result therefore does not prove successful random generation. The [OpenSSL RAND_bytes documentation](https://docs.openssl.org/3.5/man3/RAND_bytes/) describes the underlying generator and its failure status.

## Examples

```irc
$uuid4()         /* A new UUID, such as 550e8400-e29b-41d4-a716-446655440000. */
$uuid4(nodashes) /* A new 32-character identifier, with no hyphens. */
```

## Source

Registered and implemented as `function_uuid4` in [source/functions.c](../../source/functions.c). Generation is implemented by `uuid4_generate_internal` in [source/ircaux.c](../../source/ircaux.c). Crypto-library requirements are in [configure.ac](../../configure.ac).
