# $json_implode()

## Synopsis

```text
$json_implode(<var>)
$json_implode({"root":"<var>","compact":true})
```

## Technical

Serializes an assign variable structure into JSON. The normal form takes one root variable name; additional arguments are ignored. Bracket notation is normalized to dotted structure notation. Missing input or a root without global assign descendants returns the empty string, even when the root itself has a scalar value.

Substructures become JSON objects, and leaf values become JSON strings. A component with both descendants and its own value is serialized as an object; its scalar value is omitted. Numeric components also become object member names: `foo.0` and `foo.1` produce an object with keys `"0"` and `"1"`, not a JSON array. Numbers, booleans, and null are not inferred from leaf text.

Member discovery walks global assign structures only. However, leaf values are fetched with normal variable lookup, so a same-named local variable can shadow a discovered global leaf value. Local-only structures are not enumerated. Members follow the global symbol ordering, and their names use the stored variable spelling, generally uppercase. The root name itself is not included as an outer JSON key.

The JSON keyword form is selected only when input starts with `{`. `root` supplies the variable name. `compact: true` produces compact output; `false` produces formatted output. Unlike the normal form's compact default, omitting `compact` in the keyword form sets it to false. Malformed keyword JSON prints a parsing diagnostic and returns empty when no root is obtained. These diagnostics do not update [json_error](json_error.md).

The generator escapes quotation marks, backslashes, and control characters in string values and member names. The function does not change the source structure. Ordinary leaf lookup can still perform normal lookup behavior, including lazy-loading a stub.

## Practical

Use this to serialize string-valued script state. Exploding the output with [json_explode](json_explode.md) can reconstruct simple assign structures, but this is not a lossless round trip for every structure or original JSON document. Parent scalar values, local-only members, original JSON types, array identity, key spelling, and empty containers are not preserved. Exploding into a reused structure can also leave unrelated old members.

## Returns

The generated JSON object string, or the empty string when the root has no global assign descendants or generation fails. `json_error()` does not describe this function's failures.

## Examples

```text
@foo[client] = [EPIC]
@foo[details][description] = [EPIC rocks!]
@foo[details][protocol] = [IRC]
@foo[details][port] = 6667
echo $json_implode(foo)
```

The result contains a `CLIENT` string and a `DETAILS` object. Its `PORT` member is the JSON string `"6667"`, not a JSON number.

For formatted output, use `$json_implode({"root":"foo","compact":false})`.

## See also

[json_error](json_error.md), [json_explode](json_explode.md).

## Source

Registered and implemented by `function_json_implode`, `implode_struct_var`, and keyword parsing in [source/functions.c](../../source/functions.c). Global member enumeration and leaf lookup are in [source/alias.c](../../source/alias.c); JSON generation and escaping are in [source/cJSON.c](../../source/cJSON.c).
