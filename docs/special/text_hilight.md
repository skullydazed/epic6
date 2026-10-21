# Special text highlighting techniques

EPIC can make text stand out with bold, underline, reverse video, colors, and other attributes. Formatting controls in the text change the attributes used to display following characters. Their appearance depends on terminal capabilities, fonts, and display filtering.

## Control characters

The caret notation below describes an actual control character, not a literal caret followed by a letter. Use [chr](../functions/chr.md) in scripts to construct controls explicitly.

| Control | Decimal value | Effect |
| --- | --- | --- |
| `^B` | 2 | Toggle bold. |
| `^C` | 3 | Begin a decimal color sequence. |
| `^E` | 5 | Toggle alternate character set. |
| `^F` | 6 | Toggle blink. |
| `^O` | 15 | Reset all attributes, including colors. |
| `^P` | 16 | Toggle italic. |
| `^S` | 19 | Non-destructive space: advance without overwriting the screen cell. |
| `^V` | 22 | Toggle reverse video. |
| `^X` | 24 | Begin an extended color sequence. |
| `^_` | 31 | Toggle underline. |

The control's value is separate from the key binding used to insert it. Default bindings insert bold, blink, reverse, underline, and reset controls with their corresponding keys, but `^E` normally moves to end of line and `^X` switches channels. Scripts can change bindings. See [key bindings](key_bindings.md) and [graphics characters](graphics_chars.md).

For example, in an expansion-enabled command:

```irc
eval echo $chr(2)Bold$chr(2) ordinary $chr(31)underlined$chr(15)
```

The second bold control toggles bold back off; the final reset clears all attributes. Attributes are stored separately, so toggling underline does not inherently toggle reverse or bold. Reset and color-termination sequences are exceptions to a blanket claim that every control preserves every other attribute.

## Colors

A `^C` followed by a foreground number, optionally a comma and background number, sets colors. A leading comma can leave the foreground unchanged while selecting a background. For example, `$chr(3)04,01` selects the mapped red foreground and black background; `$chr(3),47` selects palette background 7 without changing the foreground.

The familiar codes map to these 256-color palette entries:

| Code | Palette index | Conventional appearance |
| --- | --- | --- |
| 0 | 231 | White |
| 1 | 16 | Black |
| 2 | 18 | Dark blue |
| 3 | 28 | Green |
| 4 | 196 | Red |
| 5 | 88 | Dark red |
| 6 | 90 | Purple |
| 7 | 208 | Orange |
| 8 | 226 | Yellow |
| 9 | 46 | Bright green |
| 10 | 30 | Teal |
| 11 | 51 | Cyan |
| 12 | 21 | Blue |
| 13 | 201 | Magenta |
| 14 | 244 | Gray |
| 15, 16 | 252 | Light gray |

Additional codes `30`–`37` select foreground palette indices 0–7; `40`–`47` select background indices 0–7. Codes `50`–`57` select indices 8–15 for either foreground or background. These colors are not reduced to eight canonical colors, and selecting them does not universally turn bold or blink on or off. The terminal ultimately determines the displayed palette and attribute appearance; bold is not a portable promise of a brighter foreground, nor blink of a brighter background.

Color parsing accepts only supported one- or two-digit codes. An unsupported two-digit combination can be parsed as a supported first digit followed by visible text; do not assume arbitrary numbers are consumed as one color. Code `99` is consumed without assigning a new color. A bare `^C` or `^C-1` clears foreground/background colors and also clears bold and blink. Use `^O` when you intend a complete attribute reset.

Extended `^X` sequences support palette and RGB colors; prefer [rgb](../functions/rgb.md) or the documented formatting functions to construct them rather than assuming `^C` accepts those formats.

## Filtering and ANSI sequences

[mangle_display](../set/mangle_display.md), [mangle_logfiles](../set/mangle_logfiles.md), and related mangle settings can remove selected attributes. Names such as `bold`, `color`, `reverse`, `underline`, `blink`, `alt_char`, `nd_space`, and `all_off` select the corresponding filtering behavior. See [mangle types](mangle_types.md) for the supported names and their interactions; not every internal attribute has a separately accepted setting name.

ANSI escape sequences are also recognized by display normalization. Supported sequences are converted into EPIC's attribute representation; unsupported terminal sequences are not a general pass-through drawing interface. Filtering, terminal capabilities, and logging settings can produce different results for the same text. For predictable script output, use EPIC formatting controls and reset attributes explicitly.

## Source references

- `include/irc.h`: formatting-control values.
- `source/screen.c`: color tables, color parsing, attribute toggles, ANSI normalization, and display output.
- `source/keys.c`: default key bindings and formatting actions.
- `source/ircaux.c`: mangle-option parsing.
- `source/term.c`: terminal attribute and color output.
