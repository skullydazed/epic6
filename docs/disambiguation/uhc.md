# Uhc: removed userhost completion

The former `$uhc(partial-mask)` function completed partial `nick!user@host` masks. For example, a nickname could become `nick!*@*`, a `user@host` input could gain a wildcard nickname, and a hostname pattern could gain wildcard nickname/user fields.

EPIC6 has retired `uhc()` and supplies no script replacement. Use the built-in [mask function](../functions/mask.md) for current mask construction, but note that its accepted input is different:

```text
$mask(5 nick!user@host.example)
$mask(0 user@host.example)
```

These return `nick!user@host.example` and `*!user@host.example`, respectively. Type `5` retains the supplied nickname, user, and host; type `0` uses a wildcard nickname. Other types widen different components as documented on the function page.

`mask()` requires an address containing `@`. It does not automatically interpret a bare nickname or hostname as the old `uhc()` examples did, so replacing the function name alone is insufficient. Supply explicit components according to what your script actually knows. Neither function name implies a server lookup or verification that the resulting mask identifies a particular user.

## History and source

`UPDATES` records the retirement on August 30, 2026, directing callers to `mask()`.

- `source/functions.c`: current registry omits `uhc`; `function_mask` implements the replacement's types and argument checks.
- `UPDATES` and `KNOWNBUGS`: removal and replacement guidance.
