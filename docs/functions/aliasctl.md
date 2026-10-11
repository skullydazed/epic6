# aliasctl function

## Synopsis

```text
$aliasctl(domain [start] operation operand [value])
$aliasctl(maxret [limit])
```

## Technical

`aliasctl()` provides a low-level interface to the symbol tables used by [`alias`](../commands/alias.md), [`assign`](../commands/assign.md), and [`local`](../commands/local.md). Domain and operation names are case-insensitive.

### Domains

| Domain | Meaning |
| --- | --- |
| `alias` | User command/function bodies in the global symbol table |
| `assign` | Variable values in the global symbol table for queries; assignment uses normal variable scope rules |
| `local` | Local variables visible in the current execution scope |

Domain names are recognized by their first two characters. Invalid domains, operations, or missing required domain/operation/operand arguments return the empty string. Operand parsing honors [`xdebug dword`](../commands/xdebug.md). Names for direct queries are converted to uppercase; global queries expect canonical dot notation, while local lookup and setters also process bracket notation.

### Operations

| Operation | Effect and return value |
| --- | --- |
| `get name` | Return the user alias body or variable value; empty if that component is absent. Built-in implementation bodies are not returned. |
| `getpackage name` | Return the corresponding user component's package tag, or empty if unavailable. |
| `set name value` | Set the value/body from the entire remaining text; return `1` after dispatching the setter. |
| `setpackage name package-name` | Replace the corresponding package tag on an existing symbol; return `1`, or `0` if no symbol is found. The entire remainder is the tag. An empty remainder clears it. |
| `exists name` | Return `1` if symbol lookup finds a record, otherwise `0`; see the shared-symbol limitation below. |
| `match prefix` | Return names beginning with `prefix`, excluding names with a further dot after that prefix. `*` means an empty prefix and lists top-level names. |
| `pmatch pattern` | Return names matching a wildcard pattern, including dotted descendants. `*` lists all user components in the selected global domain. |
| `rmatch prefix` | Currently behaves like `match`; reverse order is not implemented for this operation. |
| `rpmatch pattern` | Like `pmatch`, but traverse the symbol table in reverse order. |

`getpackage` and `setpackage` require four matching initial characters; other ordinary operations accept their first letter. `rmatch` and `rpmatch` require the first two characters, `rm` and `rp`. Use full names for clarity. `match`, `rmatch`, `pmatch`, and `rpmatch` return the empty string for the `local` domain.

### Matching limits

An optional nonzero integer between the domain and operation sets a starting offset. For `pmatch` and `rpmatch`, a positive offset skips that many matching entries before returning names. A negative offset skips every match. Zero is not recognized as an offset and produces an invalid operation instead.

`$aliasctl(maxret)` returns the current persistent match limit, initially `0` (unlimited). `$aliasctl(maxret limit)` sets it and returns the previous limit. A positive limit caps `pmatch`/`rpmatch` results; a negative limit stops after the first returned match. The start offset, limit, and reverse flag are ignored by `match`/`rmatch`. Matching lists include only symbols with a user body/value, not built-in-only records; stub definitions can appear in these lists without being loaded.

## Practical and restrictions

Use `get` to inspect an alias body and `match` or `pmatch` to discover names. Prefer normal assignment and alias commands when their syntax is clearer.

Global symbols can hold a variable, user alias, built-in command, and built-in function under the same name. `exists` checks for the shared record, rather than the selected user component. Therefore `$aliasctl(alias exists join)` can return `1` for the built-in `join` even when no user alias overrides it, and an assign-only name can also satisfy an alias existence query. Use `get` when you need a body/value, remembering that an empty component is indistinguishable from an absent one through that result.

Global direct lookups (`get`, `exists`, and package operations), and alias replacement, can resolve [`stub`](../commands/stub.md) definitions by loading their files. A query can therefore execute a script and change symbols; it is not necessarily a passive inspection. Local lookup follows visible scope and can create a field implicitly under a declared local structure.

`set` is not a validation result: it returns `1` even when the variable setter reports an invalid name. Assign-domain writes can update a visible local variable; use the `::` name prefix to force a global assignment. An empty global variable value deletes that variable, while an empty local value remains a local value. Alias-domain writes create or replace a body, including an empty body, clear stub state, and discard the alias's argument list. Alias replacement also produces the usual alias-added message. Ordinary variable writes are quiet.

New or replaced global user components can acquire the current [`package`](../commands/package.md) tag. `setpackage` changes the tag directly and does not load a package or validate that a component of the selected domain exists; it only requires a symbol record. Local package tags do not turn locals into global resources managed by [`unload`](../commands/unload.md).

## Examples

```text
$aliasctl(alias get join)
$aliasctl(alias set join //channel)
$aliasctl(assign match foo)
$aliasctl(alias match foo.)
$aliasctl(alias pmatch foo*)
```

The first returns the user `join` body, if any. The second replaces it with `//channel` and returns `1`. `match foo` can include `foobar` but excludes `foo.bar` and `foobar.baz`; `match foo.` can include `foo.bar` but excludes `foo.bar.baz`. `pmatch foo*` can include all of those names. Returned names use their canonical uppercase spelling.

## Source

[source/functions.c](../../source/functions.c) registers `aliasctl` and delegates to `aliasctl()` in [source/alias.c](../../source/alias.c), which implements scope lookup, setters, stubs, and match operations. [include/words.h](../../include/words.h) defines function argument parsing, and [source/reg.c](../../source/reg.c) implements wildcard matching.
