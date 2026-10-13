# tolower

## Synopsis

`$tolower(<text>)`

## Technical

Returns a copy of the entire argument with supported uppercase code points mapped to lowercase. Spaces, literal quotation marks, and characters without a lowercase mapping remain. There is no word parsing or special JSON form, and the original variable is not changed.

Unicode mapping uses the system's `towlower_l` with EPIC's dedicated `en_US.UTF-8` locale. If that locale could not be created, non-ASCII code points remain unchanged and ASCII uses the C lowercase operation. This is not a selection of the user's language-specific locale.

## Practical

Use this function to convert uppercase letters in text to lowercase. [`toupper`](toupper.md) converts in the other direction. These operations are not full Unicode case folding or normalization, and lowercase output alone does not implement an IRC server's nickname/channel case-matching rules.

## Returns and restrictions

The processed string, or the empty string for empty input. There are no options, settings, or server requirements.

Conversion is performed in place on the copy and only applied when the mapped code point occupies exactly the same number of UTF-8 bytes as the original. A mapping that changes byte length is skipped and a diagnostic is printed; therefore some uppercase characters can remain. Multi-character lowercase expansions are not performed. Malformed UTF-8 is not reliably processed or validated; use valid UTF-8 text rather than binary data.

## Examples

```irc
$tolower(HELLO THERE.)          Returns hello there.
$tolower(MaRY Had A LITTe lAMB) Returns mary had a little lamb.
$tolower()                     Returns the empty string.
```

## History

First appeared in ircII.

## Source

Handler: [`source/functions.c`](../../source/functions.c:2337). In-place mapping and length checks: [`strmap`](../../source/ircaux.c:1142), [`lower`](../../source/ircaux.c:1222). Locale and lowercase mapping: [`source/recode.c`](../../source/recode.c:1166).
