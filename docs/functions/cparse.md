# $cparse()

## Synopsis

`$cparse(<text>)`

`$cparse("<text>" <arguments>...)`

## Technical

Converts embedded percent directives into EPIC color and formatting control characters. In the first form, the whole input is text. If the input begins immediately with a double quote, the quoted text is parsed as one double-quoted word and then expanded using the remaining arguments for `$0`, `$1`, `$*`, and related references. This grouping does not depend on `/xdebug dword`.

Ordinary function-call expansion happens before `cparse()` receives its input. Double the dollar signs in a quoted template when the references should be expanded by `cparse()` instead. Percent directives are converted **before** that additional expansion, so percent codes introduced by template arguments are not converted again.

| Normal foreground | Bright foreground | Background | Color |
| --- | --- | --- | --- |
| `%k` | `%K` | `%0` | Black |
| `%r` | `%R` | `%1` | Red |
| `%g` | `%G` | `%2` | Green |
| `%y` | `%Y` | `%3` | Yellow |
| `%b` | `%B` | `%4` | Blue |
| `%m`, `%p` | `%M`, `%P` | `%5` | Magenta (purple) |
| `%c` | `%C` | `%6` | Cyan |
| `%w` | `%W` | `%7` | White |

Normal foreground directives emit Control-C followed by `30` through `37`; bright foreground directives emit `50` through `57`. Background directives emit Control-C followed by `,40` through `,47`. Current EPIC maps these to the corresponding basic or bright palette colors; they do not themselves insert a bold toggle.

| Directive | Meaning |
| --- | --- |
| `%F` | Insert the flashing/blink toggle (Control-F). |
| `%n` | Reset colors and all text attributes. |
| `%N` | Omit the automatically appended color reset; emits no character. |
| `%X` | Insert a literal Control-X, for an extended color sequence. |
| `%%` | Insert a literal percent sign. |

Unless `%N` occurs, the function appends Control-C followed by `-1`, even when no color directive appears or the text is empty. This resets colors and clears bold/blink when displayed by EPIC; `%n` additionally emits the all-attributes-off character. Unknown directives such as `%q` remain literal. A trailing lone `%` remains in the returned text but prevents the appended reset from being included in the returned string; use `%%` for a trailing literal percent sign.

## Practical

Use this function to add color to scripts without writing literal control characters. Ordinary text, including UTF-8, is copied unchanged apart from the directives and any quoted-template expansion.

## Returns

Returns the converted text, including control characters and the automatic reset where applicable. It does not display the result itself. Appearance depends on the terminal palette, fonts, color support, and handling of attributes such as blinking.

## Examples

In the notation below, `^C` and `^X` represent actual Control-C and Control-X characters, not two literal printable characters.

| Expression | Returned text |
| --- | --- |
| `$cparse(bl%Bah)` | `bl^C54ah^C-1` |
| `$cparse("This is a $$0 test" cparse)` | `This is a cparse test^C-1` |
| `$cparse(%X80Testing)` | `^X80Testing^C-1` |
| `$cparse(%rred%N)` | `^C31red` |
| `$cparse(100%%)` | `100%^C-1` |

The extended-color example requests palette color 80 when rendered by EPIC. `%X` only inserts the control character; the following text must form a valid extended-color sequence.

## Source

Registered and implemented in [source/functions.c](../../source/functions.c). Control characters are defined in [include/irc.h](../../include/irc.h); color mapping and rendering are in [source/screen.c](../../source/screen.c). Quoted-template expansion uses [source/alias.c](../../source/alias.c).
