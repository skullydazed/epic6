# symbolctl

## Synopsis

```irc
$symbolctl(TYPES)
$symbolctl(PMATCH <type> <pattern>)
$symbolctl(CREATE <symbol>)
$symbolctl(DELETE <symbol> [<type>])
$symbolctl(CHECK <symbol>)
$symbolctl(GET <symbol> LEVELS)
$symbolctl(GET <symbol> <level> [<type> [<property>]])
$symbolctl(SET <symbol> <level> <type> <property> [<value>])
```

## Description

A low-level interface to the unified global symbol table. A single name can contain an alias, variable, built-in command, function, expando, and `/set` component independently. This interface does not inspect local variables, resolve normal variable scope prefixes, or trigger stub loading. Names are uppercased and looked up exactly. Properties and types are case-insensitive; operation names accept prefixes, but full names avoid ambiguity.

Arguments use function-word parsing. A setter's value is the remaining raw text, including literal quotation marks. Prefer ordinary [`alias`](../commands/alias.md), [`assign`](../commands/assign.md), [`stub`](../commands/stub.md), and [`set`](../commands/set.md) commands for routine changes.

## Types and operations

`TYPES` returns `ASSIGN ALIAS BUILTIN_COMMAND BUILTIN_FUNCTION BUILTIN_EXPANDO BUILTIN_VARIABLE`. `ASSIGN` means global variables, `ALIAS` means user commands/functions, and `BUILTIN_VARIABLE` means typed `/set` variables, including script-created settings.

| Operation | Behavior and return |
| --- | --- |
| `PMATCH <type> <pattern>` | Return matching uppercase names of current components. Type must be an exact type name or `*` for all symbols, including empty placeholders. Uses wildcard matching; no matches or invalid type returns empty. |
| `CREATE <symbol>` | Create an empty placeholder, returning `1`; return `0` if it already exists. |
| `DELETE <symbol> [<type>]` | Clear the named component, or all current components if type is omitted; return `1` for an existing symbol and `0` if absent or protected. Unknown type changes nothing but still returns `1` for an existing symbol. |
| `CHECK <symbol>` | Remove an empty placeholder when possible; return `1` if the symbol existed, regardless of whether removed, or `0` if absent. |
| `GET <symbol> LEVELS` | Return the number of current and saved symbol records, normally `1`. |
| `GET <symbol> <level>` | Return the component types present in that record. |

Deletion can remove built-in commands, functions, and expandos, and this interface cannot restore their C implementations. Built-in `/set` names are protected against deletion. Deleting all components may clear other components before encountering that protection and returning `0`. Saved records keep a symbol alive; metadata or stub flags alone do not prevent garbage collection by `CHECK`.

For `DELETE`, the optional type is compared against the entire remaining string: do not add extra words or quotation marks around it.

## Levels

Level `1` is the current record. Successive [`stack push`](../commands/stack.md) saves appear at levels `2`, `3`, and so on, newest first. These are saved symbol records, not local call frames; different records can contain different components. A nonexistent symbol or a level beyond the saved chain returns empty. Use positive levels; zero, negative, or nonnumeric level input currently selects the current record rather than failing.

## Alias and assign properties

| Type | Property | `GET` result and `SET` behavior |
| --- | --- | --- |
| `ALIAS`, `ASSIGN` | `VALUE` | Alias body or variable text; set raw text, or clear with no value. For a stub, this is its file list. |
| `ALIAS`, `ASSIGN` | `STUB` | Numeric lazy-loading flag; setting requires numeric text, not a filename. |
| `ALIAS`, `ASSIGN` | `PACKAGE` | Package name; set raw text, or clear with no value. |
| `ALIAS` | `ARGLIST` | Serialized argument list; setting replaces and parses it, or clears it with no value. |

Successful setters return `1`; invalid properties or nonnumeric stub flags return empty. Changing `VALUE` does not automatically reset `STUB`, package, or argument-list metadata. Changes operate directly on the selected saved record without evaluating its body or value.

`GET` with `BUILTIN_COMMAND`, `BUILTIN_FUNCTION`, or `BUILTIN_EXPANDO` returns `0` if absent or a numeric C function address if present. Treat only zero versus nonzero as meaningful. Their `SET` forms are unsupported and return empty.

## Typed setting properties

| Property | `GET` | `SET` |
| --- | --- | --- |
| `TYPE` | `STR`, `INT`, `BOOL`, or `CHAR`. | Change type and reset current data to empty for strings or zero otherwise; return `1`. |
| `DATA` | Current formatted value. | Validate and set through normal typed-setting machinery; return `0` on success or `-1` on validation error. |
| `ORIG_DATA` | Stored original value. | Unsupported. |
| `FUNC` | Numeric C callback address, or `0`. | Unsupported. |
| `SCRIPT` | Script callback body. | Replace raw code, or clear it with no value; return `1`. |
| `FLAGS`, `PENDING` | Pending callback counter. | Set numeric counter; return `1`. This controls callback suppression, not an ordinary option bitmask. |

The property is `FUNC`, not `BUILTIN`. No `BUILTIN` property is supported. A setting with a C callback cannot be changed by these setters; they return empty. This protection checks the callback, rather than all built-in setting names.

If the setting component does not exist, entering its `SET` branch creates a default `BOOL` setting with current and original values `OFF`. `SET <symbol> 1 BUILTIN_VARIABLE` therefore creates it but returns empty because no property was supplied. The symbol must already exist. Creation installs the setting on the current global symbol even when the selected saved record lacks it; use level `1` for new settings.

`DATA` accepts `ON`, `OFF`, or `TOGGLE` for booleans, nonnegative numbers for integers, and a character one display column wide for `CHAR`. Empty data clears strings, changes `CHAR` to a space, and leaves boolean/integer values unchanged. Successful changes invoke the setting's script callback unless pending suppression is active; this path does not dispatch the `/on set` hook. Configure `TYPE` before `DATA`, and avoid manipulating the pending counter unless controlling callbacks intentionally.

## Examples

```irc
$symbolctl(PMATCH BUILTIN_COMMAND li*)    Returns LICENSE LINKS LIST.
@ symbolctl(CREATE frobnitz)
@ symbolctl(SET frobnitz 1 BUILTIN_VARIABLE TYPE BOOL)
@ symbolctl(SET frobnitz 1 BUILTIN_VARIABLE DATA ON)
$symbolctl(GET frobnitz 1 BUILTIN_VARIABLE DATA)    Returns ON.
@ symbolctl(DELETE booya ALIAS)
```

## Returns and restrictions

Missing required arguments, unknown operations/properties, and failed lookups generally return empty. An empty result can also mean an absent value or a successful setting-creation side effect; use the operation-specific conventions above. Direct setters bypass ordinary alias/assign bookkeeping, and malformed argument lists can print parser diagnostics. No server connection is required.

## Source

Wrapper: [`source/functions.c`](../../source/functions.c:8215). Dispatch and global records: [`source/alias.c`](../../source/alias.c:3427). Garbage collection: [`GC_symbol`](../../source/alias.c:1013). Setting validation and callbacks: [`source/vars.c`](../../source/vars.c:695).
