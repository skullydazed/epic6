# addset

## Synopsis

```text
/load addset
/addset <name> <bool|str|int|char> [{ <commands> }]
/delset <name>
```

## Summary

Provides the script commands `addset` and `delset` for creating and deleting user-defined [set](../commands/set.md) variables. The bundled `builtins` script loads it for its own settings.

## Description

Created settings participate in normal set lookup, display, assignment, validation, and variable expansion. This does not give them a new C implementation: any additional behavior is supplied by the optional script callback.

Choose a distinct variable name beginning with a letter and containing letters, digits, or underscores. Names are case-insensitive. The alias itself does not perform comprehensive name validation or protect against collisions; avoid existing settings.

| Type | Assignment behavior | Initial value after type creation |
| --- | --- | --- |
| `bool` | `on`, `off`, or `toggle`. | `off` |
| `str` | Text, excluding embedded NUL. Can be unset. | Unset |
| `int` | Nonnegative integer, stored in a C `int`; not an arbitrary-precision value. | `0` |
| `char` | First Unicode code point, which must occupy exactly one display column. Remaining text is ignored. | Numeric zero until explicitly assigned; assign a valid character before use. |

Initialize the setting explicitly after creation, especially for `char`. The integer parser does not promise the former unsigned 32-bit range; out-of-range conversion is not a supported way to store larger numbers.

## Callback

When a valid assignment is accepted, EPIC runs the stored callback with the setting's string representation as its argument string. The new value is already stored. For a multiword string use `$*` or `$0-`, rather than assuming the whole value is `$0`. An unset string supplies an empty argument value.

Assigning the same value can still run the callback. Querying without a value does not run it. During the callback, ordinary output is temporarily suppressed, and the setting is marked pending to prevent recursively invoking its own callback. Other side effects are still possible. Creation alone does not invoke the callback; a following `set` initializes through the normal path.

## Existing settings, deletion, and errors

Repeating `addset` for an existing user setting resets its type/value; supplying callback text replaces its script. Omitting callback text does not explicitly clear an existing callback. This is not an idempotent declaration that preserves the old value.

`delset` deletes a user-defined setting and its callback but cannot delete a C built-in setting. The lower-level setter rejects changes to a built-in variable with a C callback; this alias does not provide a safe general-purpose wrapper for redefining other built-ins.

The aliases discard the lower-level return values and generally give no success result or detailed diagnostics. Missing arguments can do nothing. An invalid type is not reliably a clean no-op: creating a new symbol can leave the initially created boolean setting before type validation fails. Use the documented type names and inspect the setting afterward. Normal `set` assignments report type-validation errors.

## Example

```text
/load addset
/addset example_label str { xecho -b New label: $*; }
/set example_label Evening session
/set example_label
/delset example_label
```

The assignment stores the text and calls the callback. The query displays it, and deletion removes this user-defined setting. These local operations send no IRC request unless callback commands do so.

## History

Written for EPIC5 by blackjac to supersede the temporary `set -create` feature.

## Source references

- [script/addset](../../script/addset): aliases delegate creation, typing, callback storage, and deletion to `symbolctl`.
- [source/alias.c](../../source/alias.c): `symbolctl` built-in-variable attributes, initial allocation, resets, callback storage, and deletion protection.
- [source/vars.c](../../source/vars.c): type validation, accepted-assignment callbacks, value arguments, output suppression, and pending guard.
- [script/builtins](../../script/builtins): loads and uses `addset`.
