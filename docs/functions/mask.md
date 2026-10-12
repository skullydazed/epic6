# $mask()

## Synopsis

```text
$mask(<type> <address>)
$mask(<address> <type>)
```

## Technical

Returns a wildcard mask derived from an address in `nick!user@host` or `user@host` form. When the nickname is omitted, it is treated as `*`. The function performs string manipulation locally; it does not resolve names or query a server.

In the first form, the address is the entire remaining text and is not a quoted word. In the second form, the address is a quoted word and surrounding double quotes are removed; the type follows it. A decimal numeric first argument selects the first form. Types are parsed with EPIC's integer conversion, so use the decimal values below without leading zeroes.

The mask types are:

| Type | Result |
| --- | --- |
| 0 | `*!user@host` |
| 1 | `*!*user@host` |
| 2 | `*!*@host` |
| 3 | `*!*user@domain-mask` |
| 4 | `*!*@domain-mask` |
| 5 | `nick!user@host` |
| 6 | `nick!*user@host` |
| 7 | `nick!*@host` |
| 8 | `nick!*user@domain-mask` |
| 9 | `nick!*@domain-mask` |
| 10 | `*!*user@*` |

For types 1, 3, 6, and 8, a username longer than seven bytes loses its first byte before the leading `*` is added. Type 10 keeps the complete username. There is no general removal of leading tildes or replacement of digits within hostnames.

The `domain-mask` is derived by these rules, in order:

- A host containing at least two colons keeps the text through its second colon and appends `*`: `2001:db8::1` becomes `2001:db8:*`.
- A host containing only digits and exactly three dots keeps the text through its third dot and appends `*`: `192.0.2.15` becomes `192.0.2.*`.
- Other hosts with at least two dots normally keep their last two labels, preceded by `*.`. If the suffix starting at the second-to-last dot is shorter than seven bytes, the function keeps three labels when available. Thus `long.host.blah.com` becomes `*.blah.com`, while `a.b.co.uk` becomes `*.b.co.uk`. A two-dot hostname with such a short suffix is kept intact.
- Other hosts are kept intact.

These are textual heuristics, not IP address validation or a public suffix lookup.

## Returns

The resulting mask, or an empty string when the address has no `@`, a required argument is missing, or the type is outside 0–10. Types 11–13 are not implemented. Empty user or host portions are not rejected. Very long results are truncated to EPIC's built-in buffer limit.

## Examples

```text
$mask(3 foo!bar@long.host.blah.com)              returns *!*bar@*.blah.com
$mask(10 nick!user@ppp-147-0-52-129.frobitz.com) returns *!*user@*
$mask(nick!user@192.0.2.15 9)                  returns nick!*@192.0.2.*
$mask(0 nick!~user@example.com)                returns *!~user@example.com
$mask(1 nick!abcdefgh@example.com)             returns *!*bcdefgh@example.com
```

## Restrictions

The username adjustment is byte based and can cut a multibyte character. The address is not otherwise normalized or checked for valid IRC mask syntax. Choose the mask type according to the scope you intend to match.

Implementation: [function_mask()](../../source/functions.c), with argument and integer helpers in [ircaux.c](../../source/ircaux.c) and [functions.h](../../include/functions.h).
