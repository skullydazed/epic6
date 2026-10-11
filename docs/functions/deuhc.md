# $deuhc()

## Synopsis

`$deuhc(<nick!user@host>)`

## Technical

Removes a leading literal `*!` or `*!*@` from an address or mask. The input must contain both `!` and `@` somewhere; otherwise the function returns an empty string, even if it already resembles `user@host` or a hostname.

If the input starts with `*!`, those two characters are removed. If the remaining text then starts with `*@`, those characters are removed too. Other prefixes, including an actual nickname such as `Alice!`, are left unchanged. No wildcard matching or address validation occurs.

The whole input is processed as text. There is no word parsing, dequoting, or whitespace trimming: leading spaces or quotes prevent the literal prefix removal. No character-set conversion is performed.

## Practical

Use this function to shorten masks that begin with wildcard nickname or username components. It extracts a hostname from a mask such as `*!*@host`, but it is not a general hostname extractor for arbitrary `nick!user@host` addresses.

## Returns

Returns the input with the supported prefix removed, or unchanged if both required punctuation characters are present but no supported prefix occurs. Returns an empty string if either `!` or `@` is absent, including empty input.

## Examples

| Expression | Result |
| --- | --- |
| `$deuhc(*!user@host)` | `user@host` |
| `$deuhc(*!*@host)` | `host` |
| `$deuhc(*!*@*.host)` | `*.host` |
| `$deuhc(Alice!user@host)` | `Alice!user@host` |
| `$deuhc(user@host)` | Empty string |

## Source

Registered and implemented in [source/functions.c](../../source/functions.c). The implementation checks for both punctuation characters, then removes only the two literal prefixes described above.
