# $json_error()

## Synopsis

`$json_error()`

## Technical

Returns the stored parse-error string from [json_explode](json_explode.md). It accepts no arguments; supplied text is ignored. Reading the error does not clear it.

When `json_explode` reaches the parser and parsing fails, it replaces the error with `JSON parse error at position N`. `N` is the parser's zero-based byte offset within the JSON text passed after the variable name, not a character index or an offset within the whole function call. It marks the parser's failure position, rather than necessarily identifying the original cause.

A successful parse clears the stored error. Before any failed parse, the result is empty. A `json_explode` call missing its required variable name returns early without clearing or replacing a previous error. Other JSON parsing operations, including JSON keyword-argument parsing, and [json_implode](json_implode.md) do not update this stored error.

The state is shared across calls, rather than stored separately for each alias or variable. Save it promptly if subsequent calls might replace it. The returned string is a copy; the original JSON input is not retained in the error message.

## Returns

An error string, or the empty string when no parse error is stored. This is not a general validation result: it reports only the error state maintained by `json_explode`, and a successful parse clears it even if you have other reasons to reject the data.

## Examples

```text
@parsed = json_explode(data not_json)
@parse_error = json_error()
echo $parse_error
```

The invalid JSON stores a parse error, and the second assignment saves its message without clearing it.

## See also

[json_explode](json_explode.md), [json_implode](json_implode.md).

## Source

Registered and implemented as `function_json_error` in [source/functions.c](../../source/functions.c), beside `json_last_error` and `function_json_explode`. Parser offsets are maintained by `cJSON_Parse` in [source/cJSON.c](../../source/cJSON.c). Return-string copying is defined in [include/functions.h](../../include/functions.h).
