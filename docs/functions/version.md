# version

Return the compatibility string `2.8`.

## Synopsis

```irc
$version([<server description>])
```

## Technical

This function is obsolete. It once distinguished IRC server classes so scripts could adjust their behavior. The current implementation always returns `2.8`: it does not look up a server, examine a connection, or inspect the supplied text. All arguments are ignored, including invalid server descriptions. Calling it without arguments has the same result.

## Practical

New scripts should use the server's advertised features rather than this fixed compatibility value. Use [serverctl](serverctl.md) to read a cached ISUPPORT (`005`) token or the cached server version string:

```irc
$serverctl(get -1 005 CHANTYPES)
$serverctl(get -1 version)
```

These queries use the current execution server. The cached version normally comes from the server's `004` registration reply and can also be changed locally through `serverctl`; it is not obtained by `$version()`. The [version command](../commands/version.md) is a separate command. This function does not identify the EPIC client version.

## Returns

Always the literal string `2.8`, whether connected or disconnected.

## Examples

```irc
$version()             /* 2.8 */
$version(2)            /* 2.8 */
$version(no-such-host) /* 2.8 */
```

## History

The function first appeared in EPIC2pre3 and was deprecated in EPIC5-0.0.2.

## Source

Registered and implemented as `function_server_version` in [source/functions.c](../../source/functions.c). Cached server properties are exposed by `serverctl` in [source/server.c](../../source/server.c), and the `004` version cache is updated in [source/numbers.c](../../source/numbers.c).
