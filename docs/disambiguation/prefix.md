# Prefix

For the common initial substring of a word list, see the [prefix function](../functions/prefix.md):

```text
$prefix(word list)
```

It compares words case-insensitively and returns the shared beginning using the first word's spelling. Missing input or no common beginning returns an empty string. It does not modify the input variables.

Examples:

| Expression | Result |
| --- | --- |
| `$prefix(foobar foobaz)` | `fooba` |
| `$prefix(foobar foobaz foobooya)` | `foob` |
| `$prefix(one two three)` | Empty string |

A completion script can filter candidate nicknames, apply `prefix()` to those candidates, and insert the result into the input line. The function itself does not install a completion binding or edit the input. See [onchannel](../functions/onchannel.md), [pattern](../functions/pattern.md), and [key bindings](../special/key_bindings.md).

Quoted-word behavior depends on `xdebug dword`; long candidate lists and shared prefixes can require repeated comparisons. Consult the function page for result quoting and Unicode details.

For status-line prefixes instead, see the `%{1}P` field in [status_format](../set/status_format.md). It is unrelated to common-substring calculation.

## Source references

- `source/functions.c`: `prefix` registration and common-prefix calculation.
- `source/status.c`: separate status-window prefix field.
