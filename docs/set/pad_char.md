# set pad_char

## Synopsis

```text
/set pad_char [<character>]
/set -pad_char
```

Default: a space. With no argument, [set](../commands/set.md) displays the current value. `/set -pad_char` restores a space.

## Description

Selects the character used to pad width-qualified expansions, such as `$[8]name` or `$[-8]name`, instead of spaces. Positive widths left-align the expanded text and add padding on the right; negative widths right-align it and add padding on the left. Thus the setting applies to both alignment directions.

The value is a character, not a repeating string. Only the first Unicode code point supplied is used; later characters are ignored. That character must occupy exactly one display column according to EPIC's width calculation. An undetermined character or one with another width produces an error and leaves the previous value unchanged. This excludes combining characters and double-width characters as padding choices.

Width formatting measures display columns, interpreting formatting attributes, rather than simply counting bytes. It also truncates overlong text to the requested width. A zero width does not invoke this padding or truncation pass. Changing the padding character affects later expansions and does not change existing text or the normal spaces used elsewhere in EPIC's display.

## Example

```text
/set pad_char .
/assign label abc
/echo $[6]label
/echo $[-6]label
```

The first expansion is `abc...`; the second is `...abc`. Restore normal space padding with `/set -pad_char`.

For formatting with an explicit fill character instead of changing this global default, see [fix_width](../functions/fix_width.md).

## Source references

- [source/vars.c](../../source/vars.c): character registration, first-code-point decoding, one-column validation, and unset behavior.
- [include/config.h](../../include/config.h): space default.
- [source/expr.c](../../source/expr.c): `TruncateAndEscape` selects alignment and reads `pad_char` for nonzero expansion widths.
- [source/ircaux.c](../../source/ircaux.c): `fix_string_width` normalizes text, measures columns, truncates, and adds the fill character.
