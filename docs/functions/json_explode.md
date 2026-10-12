# $json_explode()

## Synopsis

`$json_explode(<var> <json data>)`

## Technical

Deserializes JSON data into assign variables rooted at `<var>`. The variable name is the first function argument; the remaining input is passed directly to the JSON parser. Do not put an extra pair of quotes around the complete JSON text. The variable name is required.

Objects become dotted variable structures: a member named `width` under `foo.format` becomes `foo.format.width`, accessible as `$foo[format][width]`. Member names are processed byte by byte, replacing characters that fail the C alphanumeric test with underscores. Normal variable names are case-insensitive, so distinct JSON keys can collide after case folding or replacement; later assignments overwrite earlier ones. Non-ASCII names do not have a reliable Unicode-preserving mapping.

Arrays use zero-based numeric components such as `foo.0` and `foo.1`. No array-length or JSON-type metadata is stored. Empty objects and arrays make no assignments. Existing root values and unrelated members are not cleared, so reusing a structure can retain stale data.

| JSON value | Variable handling |
| --- | --- |
| String | Decoded string, truncated at an embedded Unicode NUL. |
| Number | Converted to a C double and formatted according to [floating_point_math](../settings/floating_point_math.md) and [floating_point_precision](../settings/floating_point_precision.md). With floating-point math disabled, the fractional part is discarded. |
| `false` | Assigned `0`. |
| `null` | Assigned an empty value; a global assign is removed. |
| `true` | Currently unhandled: prints `Odd cJSON type in $json_explode` and leaves that target unchanged. |

The current implementation mistakenly tests `false` twice, so it does not assign `1` for `true`. An empty JSON string also removes a global assign. Assignments use normal variable rules: existing local variables can receive values, a `:` prefix requests local assignment, and `::` requests global assignment. Assignment-name errors can produce diagnostics without making the function return failure.

The complete JSON tree is parsed before assignments begin. Parse failure leaves destination variables unchanged and records an error for [json_error](json_error.md). A successful parse clears that error, then applies assignments. The parser accepts a leading JSON value without requiring the remaining input to be exhausted, so success does not certify that the entire supplied text is valid JSON. Container nesting is limited to 1000 by default (`CJSON_NESTING_LIMIT`).

## Returns

| Result | Meaning |
| --- | --- |
| `1` | Parsing succeeded and the tree was traversed; individual assignment failures or unhandled `true` values do not change this result. |
| Empty string | Required variable name is missing, or parsing failed. |

A missing variable name returns before updating the stored JSON error. Capture `json_error()` promptly after a parse failure.

## Examples

```text
@json = [{"name":"Jack (\"Bee\") Nimble","format":{"type":"rect","width":1920,"height":1080,"interlace":false,"frame rate":24}}]
if (json_explode(foo $json)) {
    echo $foo[format][width]
} else {
    echo JSON parse error: $json_error()
}
```

This assigns `foo.format.width` to `1920`, `foo.format.interlace` to `0`, and `foo.format.frame_rate` to `24`.

## See also

[json_error](json_error.md), [json_implode](json_implode.md). Exploding and imploding do not preserve every JSON type or member name.

## Source

Registered and implemented by `function_json_explode` and `explode_json_object` in [source/functions.c](../../source/functions.c). Parsing is in [source/cJSON.c](../../source/cJSON.c), assignment rules are in [source/alias.c](../../source/alias.c), and number formatting is in [source/ircaux.c](../../source/ircaux.c).
