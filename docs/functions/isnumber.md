# $isnumber()

## Synopsis

```text
$isnumber(<text>)
$isnumber(b<base-num> <text>)
$isnumber(<text> b<base-num>)
```

## Technical

Tests whether the number argument can be consumed as one integer segment, optionally followed by a literal decimal point and a second integer segment. At least one segment must contain a number, and the entire number argument must be consumed. The function tests syntax; it does not return the numeric value.

The default base is `0`, which auto-detects decimal, leading-zero octal, and `0x` hexadecimal syntax. With an explicit base, digits must be legal in that base. If a decimal point occurs with base `0`, the segment after it is parsed in base `10`; otherwise both segments use the chosen base.

A lowercase `b` introduces the base argument. It can be the first or second word. In first position it is recognized only when its suffix contains decimal digits, optionally preceded by `-`; otherwise that word is treated as the number. In second position any word starting with lowercase `b` is treated as a base argument, using base-detecting integer conversion of its suffix. Bases outside `0` through `36` revert to `0`. Use `0` or `2` through `36`: the implementation also passes `1` to the C library, where it is an unsupported base with no reliable result.

The first word and a second-position base are ordinary unquoted words. A number following a first-position base uses function-argument word parsing. Text beyond the number and optional base is ignored; this does not validate a whole multiword input string.

Each segment uses the C integer parser, which accepts a leading sign. Thus `+3`, `-3`, `.5`, `3.`, and even `3.-4` qualify. A bare `.` does not. Exponent notation, `inf`, and `nan` are not accepted. Overflow is not checked, so `1` does not establish that the value fits an integer or is a valid floating-point literal.

## Practical

Use this to check number syntax in an indicated base, such as octal or hexadecimal. Its two-segment rules differ from a normal floating-point parser, so do not treat it as a general numeric conversion or range check.

## Returns

`1` if the number argument passes the syntax test, otherwise `0`. Missing or empty number input returns the empty string.

## Examples

| Expression | Result |
| --- | --- |
| `$isnumber(10)` | `1` |
| `$isnumber(hello)` | `0` |
| `$isnumber(0xdeadbeef)` | `1` |
| `$isnumber(b8 0xdeadbeef)` | `0` |
| `$isnumber(b8 07f932)` | `0` |
| `$isnumber(3.4)` | `1` |
| `$isnumber(b16 a.f)` | `1` |
| `$isnumber(a.f)` | `0` |
| `$isnumber(0xa.0)` | `1` |
| `$isnumber(.5)` | `1` |
| `$isnumber(3.-4)` | `1` |
| `$isnumber(1e3)` | `0` |

## Source

Registered and implemented as `function_isnumber` in [source/functions.c](../../source/functions.c). First-position base recognition (`is_number`) and base conversion (`my_atol`) are in [source/ircaux.c](../../source/ircaux.c).
