# $jsontest()

## Synopsis

`$jsontest({"name1":"<value>","flag":"<value>"})`

## Technical

A diagnostic function for EPIC's JSON keyword-argument parser. It prints the converted `name1` and `flag` fields and always returns the empty string. It does not create assign variables or change the stored error returned by [json_error](json_error.md).

Input must start immediately with `{`. Otherwise it prints `jsontest: invalid input (must be json object)`. The complete supplied input is passed to the parser; malformed JSON prints an `Error before:` diagnostic. The parser does not require input after the parsed JSON value to be exhausted.

On successful parsing, the function prints `name1 is <value>` followed by `flag is <value>`. Extra fields are ignored. Both fields use string conversion, even though one is named `flag`:

| JSON field value | Printed conversion |
| --- | --- |
| String | Decoded string, truncated at an embedded NUL. |
| Number | Fixed-point `%Lf` formatting, normally six fractional decimal places. |
| `true` / `false` | `1` / `0`. |
| `null` or missing field | Empty string. |
| Array | `<unsupported array>`. |
| Object | `<unsupported dict>`. |

Although the fields are marked required internally, missing fields are converted to empty strings and still printed. No success or failure Boolean is returned. The ordinary `say` output path is used, so display suppression and output hooks can affect what is visible.

## Practical

Use this to inspect keyword-argument conversions. For deserializing JSON into assign variables, see [json_explode](json_explode.md); this diagnostic function does not perform that operation.

## Returns

Always the empty string, including on successful parsing.

## Examples

```text
@ignored = jsontest({"name1":"Alice","flag":true})
```

This prints `name1 is Alice` and `flag is 1`, subject to ordinary output handling. `$jsontest({})` prints both labels with empty values.

## Source

Registered and implemented by `function_jsontest` and `parse_kwargs` in [source/functions.c](../../source/functions.c). JSON parsing is in [source/cJSON.c](../../source/cJSON.c), and `say` output handling is in [source/output.c](../../source/output.c).
