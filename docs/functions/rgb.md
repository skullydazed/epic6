# rgb function

## Synopsis

`$rgb(<foreground> [background])`

`$rgb({"r":255,"g":0,"b":0,"bg_r":0,"bg_g":0,"bg_b":0,"attr":true})`

## Technical

Constructs an EPIC 24-bit RGB color sequence. Each foreground or background color can be supplied as `#RRGGBB`, three comma-separated decimal components such as `255,0,0`, or three separate integer arguments such as `255 0 0`. Separate the foreground and background by whitespace. These forms can be mixed. A hyphen skips a color, and the background can be omitted; use `-` as the foreground to specify only a background.

The ordinary form accepts component values from 0 through 255. A color with an out-of-range component is omitted rather than clamped. Separate integer components use EPIC's base-aware integer parsing; comma-separated components use decimal conversion. Numeric text is not strictly validated for trailing garbage. A hexadecimal token needs six valid hex digits after `#`; missing or invalid digits return empty, while extra characters after those six digits are ignored. Extra arguments after the two colors are ignored.

## Options

Input beginning with `{` after leading whitespace selects the JSON form. `r`, `g`, and `b` specify foreground components; `bg_r`, `bg_g`, and `bg_b` specify background components. A color is included only when all its components are nonnegative. Unlike the ordinary form, JSON values above 255 are reduced to their low eight bits. Missing component fields leave that color incomplete; the parser can report missing-field diagnostics.

JSON `attr` defaults to true. When false, the return contains only the hexadecimal foreground/background pair without the color control or surrounding braces. Malformed JSON produces a parser diagnostic but the wrapper continues with its initial values, normally yielding a sequence with both colors omitted.

## Returns

Normally a literal extended-color control byte (`0x18`, conventionally written `^X`) followed by `{#RRGGBB,RRGGBB}`. Each color is six uppercase hexadecimal digits. An omitted color has no digits in its position, so foreground-only red is represented as `^X{#FF0000,}`. With `attr:false`, that pair is returned as `FF0000,` instead.

Missing input or incomplete ordinary numeric arguments return empty. Skipping both colors still returns `^X{#,}`; JSON with `attr:false` returns `,` for the same omission. The result is a string: the function does not display or send it itself, and requires no IRC connection.

## Practical

Insert the returned sequence into text when selecting a specific foreground or background RGB color. Actual display depends on the terminal and EPIC's output handling; this is not a palette-index lookup. [/xecho](../commands/xecho.md) can display locally constructed text.

## Examples

Here `^X` denotes the actual control byte, not two literal characters.

| Expression | Result representation |
| --- | --- |
| `$rgb(#ff0000)` | `^X{#FF0000,}` |
| `$rgb(255,0,0 0 0 0)` | `^X{#FF0000,000000}` |
| `$rgb(- #0000ff)` | `^X{#,0000FF}` |
| `$rgb({"r":255,"g":0,"b":0,"attr":false})` | `FF0000,` |

## Source

Registration, `function_rgb()`, and JSON parsing: [source/functions.c](../../source/functions.c). Extended-color normalization and rendering: [source/screen.c](../../source/screen.c).
