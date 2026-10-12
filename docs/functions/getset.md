# getset function

## Synopsis

`$getset(<set name>)`

## Technical

Returns the current value of the named [set](../commands/set.md) setting, bypassing script variables that would otherwise shadow `$<set name>`. Both built-in settings and typed settings registered by scripts are supported. An ordinary `/assign` alone does not create a setting.

The entire argument is the setting name, matched case-insensitively and exactly. Unlike `/set`, this function does not accept abbreviated prefixes. Quotes, extra words, and spaces are literal parts of the lookup name; supply the full name without quotation marks. Script setting names must use canonical dot notation rather than bracket notation.

An omitted or unknown name returns the empty string. The function queries the stored value without changing it or invoking the setting's change callback or `/on set` hooks. Its symbol lookup can, however, load a script if a variable or command stub exists under that exact name; script loading can have its own effects or diagnostics.

## Practical

Settings such as `$display` and `$insert_mode` can be shadowed by script variables, for example `/assign display testing`. Use `$getset(display)` to retrieve the underlying setting regardless of that assignment or a local variable with the same name.

## Returns

The setting's value as a string:

| Setting type | Returned value |
| --- | --- |
| Boolean | `ON` or `OFF` |
| Integer | Decimal integer string |
| Character | The character encoded as UTF-8 |
| String | The stored text, without added quoting |

An unset or empty string setting, an unknown setting, and missing input all return empty; the result does not distinguish these cases. Normal lookup failure produces no diagnostic.

## Examples

```irc
@ actual_display = getset(display)
@ debug_level = getset(debug)
```

The first stores the underlying `/set display` value (`ON` or `OFF`); the second stores the integer `/set debug` value. These queries bypass assignments with the same names.

## History

First appeared in EPIC4pre2.400.

## Source

Registration and the direct setting query: [source/functions.c](../../source/functions.c). Name normalization and value formatting: [source/vars.c](../../source/vars.c). Exact symbol lookup, setting components, and stub loading: [source/alias.c](../../source/alias.c).
