# Ignorectl: removed ignore-rule control

The former `ignorectl()` function inspected and changed the built-in ignore table. It could enumerate rule references, add/change/delete rules, match masks in either direction, select rules by levels, and read or write individual attributes.

EPIC6 has removed that built-in table and no longer registers `ignorectl()`. The [ignore script](../scripts/ignore.md) defines a proposed `/ignore` replacement but does **not** supply `ignorectl()`, and the script itself is unfinished. Loading it does not restore this function or its historical result conventions.

## Former data and operations

The old `get`/`set` fields included nickname masks, level dispositions, suppression/exception/highlight bitmasks, expiration, reason, creation and last-use timestamps, and hit counters. None is a current built-in ignorectl record API. Historical `refnums`, `pattern`, `rpattern`, `with_types`, `get`, and `set` examples should not be used to infer current state or mutate a replacement script's private variables.

Some compatibility aliases in [builtins](../scripts/builtins.md), including `igmask`, `igtype`, `rigmask`, and `rigtype`, still call `ignorectl()`. Their definitions do not provide the missing function, so those wrappers cannot be relied on to query or enforce ignore rules.

For the removed user-facing command and its migration status, see [ignore](ignore.md). For writing explicit event/output policies, see [on](../commands/on.md), [context](../on/context.md), and [shh](../commands/shh.md). Those facilities do not automatically recreate the former rule table or precedence semantics.

## History and source

`ignorectl()` first appeared in EPIC4-1.1.13.

- `source/functions.c`: current registry lacks `ignorectl`.
- `script/ignore`: incomplete command replacement without an ignorectl alias.
- `script/builtins`: retained wrappers that still reference the absent function.
- `UPDATES` and `KNOWNBUGS`: retirement and redesign notes for ignore support.
