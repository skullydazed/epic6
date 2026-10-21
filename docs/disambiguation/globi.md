# Globi: removed case-insensitive file globbing

The former `$globi(file-pattern)` function returned matching filenames using case-insensitive pathname matching. Its pattern used double-quoted-word parsing, allowing spaces in a quoted pattern. It was intended for situations where callers could not control the capitalization of filenames, such as help-file lookup.

EPIC6 has retired `globi()` and its private glob implementation. The current function registry has no `globi` entry, and no bundled script replaces it. There is no current case-insensitive result list or error contract under that name.

For filesystem pattern expansion, see [glob](../functions/glob.md). The current implementation calls the system glob facility with `GLOB_MARK`; it does not enable a case-insensitive matching flag. Matching therefore follows the platform's pathname rules, and it is not a drop-in guarantee of the old globi behavior. EPIC's case-insensitive IRC wildcard matching is a different mechanism.

Where a script needs specific case variants, it must explicitly account for its intended filename and platform rules rather than assuming that replacing `globi` with `glob` preserves matches. Globbing reads local filesystem names and sends no IRC request.

## History and source

`globi()` first appeared in EPIC4pre2.509.

- `source/functions.c`: current function registry and system `glob()` call.
- `KNOWNBUGS`: explicit sunset of `globi()` and `glob.c`.
