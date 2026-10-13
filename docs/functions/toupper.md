# toupper

## Synopsis

`$toupper(<text>)`

## Technical

Returns a copy of the entire argument with supported lowercase code points mapped to uppercase. Spaces, literal quotation marks, and characters without an uppercase mapping remain. There is no word parsing or special JSON form, and the original variable is not changed.

Unicode mapping uses the system's `towupper_l` with EPIC's dedicated `en_US.UTF-8` locale. If that locale could not be created, non-ASCII code points remain unchanged and ASCII uses the C uppercase operation. This is not a selection of the user's language-specific locale.

## Practical

Use this function to convert lowercase letters in text to uppercase. [`tolower`](tolower.md) converts in the other direction. These operations are not full Unicode case folding or normalization, and uppercase output alone does not implement an IRC server's nickname/channel case-matching rules.

## Returns and restrictions

The processed string, or the empty string for empty input. There are no options, settings, or server requirements.

Conversion is performed in place on the copy and only applied when the mapped code point occupies exactly the same number of UTF-8 bytes as the original. A mapping that changes byte length is skipped and a diagnostic is printed; therefore some lowercase characters can remain. Multi-character uppercase expansions are not performed. Malformed UTF-8 is not reliably processed or validated; use valid UTF-8 text rather than binary data.

## Examples

```irc
$toupper(Hello there.)          Returns HELLO THERE.
$toupper(MaRY Had A LITTe lAMB) Returns MARY HAD A LITTE LAMB.
$toupper()                     Returns the empty string.
```

## History

First appeared in ircII.

## Source

Handler: [`source/functions.c`](../../source/functions.c:2313). In-place mapping and length checks: [`strmap`](../../source/ircaux.c:1142), [`upper`](../../source/ircaux.c:1204). Locale and uppercase mapping: [`source/recode.c`](../../source/recode.c:1166).
