# unveil

Restrict the filesystem view of the EPIC process on systems providing `unveil()`.

## Synopsis

```irc
$unveil(<path> <permissions>)
$unveil()
$unveil({"path":"<path>","permissions":"<permissions>"})
$unveil({"close":true})
$unveil({"test":1})
```

## Technical

The function is always registered, but calls the operating system only when configure detects `unveil()` and defines `HAVE_UNVEIL`. The JSON `test` option returns whether this support was compiled in, without making a system call. A nonzero test takes precedence over `close` and the path arguments.

In the plain form, both arguments always honor double-quoted words. Extra arguments are ignored. A path without a permissions argument returns an empty string without calling the system. Paths are passed directly: EPIC does not expand `~` or normalize them.

The JSON form accepts `path` and `permissions` strings, an integer `test`, and a boolean `close`. With `close` true, or with no plain arguments, EPIC calls `unveil(NULL, NULL)` to lock further changes. Otherwise it passes the supplied path and permissions. Missing JSON path or permissions fields remain null pointers; explicit JSON `null` values become empty strings, which are different arguments.

On OpenBSD, permissions contain zero or more of `r` (read), `w` (write), `x` (execute), and `c` (create or remove). A directory rule covers its contents unless a more specific rule applies. The first path rule restricts filesystem visibility; subsequent calls add rules or reduce permissions. Locking prevents further changes. See the [OpenBSD unveil manual](https://man.openbsd.org/unveil.2) for the operating system's precise rules and errors.

## Practical

Establish the paths EPIC needs before locking: scripts, logs, and any files needed by subprocesses, name resolution, or TLS configuration. Restrictions affect the process, rather than a single script or function call. They cannot be undone by calling this function with broader permissions. [pledge](pledge.md) provides a separate system-call restriction mechanism.

## Returns

With support compiled in, a system call returns `0` on success or `-1` on failure. EPIC does not return the system error message. Without support, ordinary calls return an empty string. The JSON test returns `1` when support is compiled in and `0` otherwise; it does not establish whether a later call would succeed.

## Restrictions

Use valid JSON carefully. The handler ignores JSON parsing failure after printing a diagnostic. Its default path and permissions then remain null pointers, so malformed JSON on a supported build can still lock further changes. An empty JSON object also calls `unveil(NULL, NULL)`; use the explicit test form for capability detection.

## Examples

```irc
$unveil({"test":1}) /* Capability check only; does not restrict access. */
```

The following calls change the process's filesystem access and finally lock its rules; adapt paths and include all required resources before using them:

```irc
@ unveil(/home/user/scripts r)
@ unveil(/home/user/irclogs rwc)
@ unveil()
```

## Source

Registered and implemented as `function_unveil` in [source/functions.c](../../source/functions.c), using `parse_kwargs` for JSON arguments. Configure-time detection is in [configure.ac](../../configure.ac); JSON type checks are in [source/cJSON.c](../../source/cJSON.c).
