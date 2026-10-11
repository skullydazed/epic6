# getcap function

## Synopsis

`$getcap(term <capname> [<nametype> [<mangle>]])`

## Technical

Queries a terminal capability from EPIC's initialized terminal database. The first argument is a capability type; currently only `term` is supported, case-insensitively. This is terminal information, rather than IRC server capability negotiation.

Capability names must match exactly and are case-sensitive. The optional name type selects which spelling to use from EPIC's capability mapping table:

| Name type | Capability spelling |
| --- | --- |
| `0` (default) | Full capability name, such as `key_f10`. |
| `1` | Abbreviated terminfo name, such as `kf10`. |
| `2` | Abbreviated termcap name, such as `k;`. |

The optional mangle value defaults to `1`. With any nonzero value, control bytes below 32 become caret notation (`^A`, `^[`, and so on), and byte 127 becomes `^?`. With `0`, the raw capability string is returned. Mangling is suitable for passing a key sequence to [/bind](../commands/bind.md), but does not escape ordinary printable characters such as `^`.

Arguments use normal function word parsing. Numeric options use integer conversion with base detection; additional arguments are ignored. There is no window or screen selector: this uses the terminal database initialized from the client's terminal environment.

## Practical

Use this to obtain a terminal key sequence or an output control string. `/bind -symbolic key_f10` already provides a shortcut for looking up a key capability using this same facility. A raw output capability can be used with [/xecho -r](../commands/xecho.md) when appropriate for the terminal.

Capability availability and values depend on the terminal database and `TERM`. Only names in EPIC's mapping table are recognized. Returned parameterized capabilities are templates; this function does not substitute parameters into them.

## Returns and restrictions

Returns the requested string capability, optionally mangled. Missing required arguments, an unsupported type or name type, an unknown name, or an unavailable capability returns the empty string.

The current implementation retrieves only string-valued capabilities. Boolean and numeric capabilities, such as `has_meta_key` or `columns`, return empty rather than their boolean or numeric values. The query itself does not emit the capability to the terminal.

## Examples

| Expression | Meaning |
| --- | --- |
| `$getcap(term key_f10)` | F10 key sequence in caret notation, if available. |
| `$getcap(term kf10 1 0)` | The same sequence as raw bytes. |
| `$getcap(term k; 2)` | The same sequence using its termcap name, mangled. |
| `$getcap(term has_meta_key 0 0)` | Empty string: boolean capabilities are not returned. |

## Source

Registration, defaults, and argument handling: [source/functions.c](../../source/functions.c). Capability name mapping, terminal initialization, string-only lookup, and control-byte mangling: [source/term.c](../../source/term.c). Symbolic key binding: [source/keys.c](../../source/keys.c).
