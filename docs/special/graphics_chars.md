# Portable use of graphics characters

Graphics such as box borders can be drawn using ordinary Unicode characters or the terminal's alternate character set. Their appearance depends on the terminal, its font, and the terminal description EPIC uses.

## Alternate character set

The alternate character set attribute is toggled by Control-E, byte `0x05`. In a script, `$chr(5)` produces that character. Put a toggle before the graphics text and another after it to turn the attribute off. The [altcharset](../bind/altcharset.md) binding can insert the same toggle into the input line.

This attribute changes how supported characters are displayed; it does not convert text to Unicode or select an input encoding. EPIC passes the attribute through the terminal's `sgr` capability, so the terminal description and emulator must support it. Some terminals may provide no useful alternate graphics characters.

`$getcap(TERM acs_chars 0 0)` reads the raw alternate-character mapping from the current terminal description. The first `0` selects long capability names, and the final `0` requests the raw value rather than printable control-character notation. An unavailable capability returns an empty string.

The mapping consists of character pairs: the first is a standard VT100 graphics name, and the second is the character this terminal description uses for it. Missing pairs mean that the mapping does not advertise that character; do not assume every terminal supplies every pair.

| VT100 name | Shape |
| --- | --- |
| `w` | Tee pointing down |
| `u` | Tee pointing left |
| `t` | Tee pointing right |
| `v` | Tee pointing up |
| `l`, `k` | Upper-left and upper-right corners |
| `m`, `j` | Lower-left and lower-right corners |
| `x` | Vertical line |
| `q` | Horizontal line |

## Example

This alias finds the terminal's horizontal-line mapping and draws a line. It falls back to ASCII hyphens if the pair is absent. The toggles are necessary: the mapping character alone does not enable alternate-character display.

```irc
alias graphics_line {
    local mapping $getcap(TERM acs_chars 0 0);
    local hline;
    fec ($mapping) vt me {
        if (vt == [q]) { @ :hline = me; };
    };
    if (hline != []) {
        echo $chr(5)$repeat(20 $hline)$chr(5);
    } else {
        echo --------------------;
    };
}
```

Invoke `/graphics_line` after defining it. A box can be assembled in the same way by retrieving all four corners plus the horizontal and vertical lines. Check that every required mapping exists, and reset the attribute after each graphics segment so ordinary message text is not displayed using the graphics set.

## Unicode and display settings

For a UTF-8 terminal and a font with the necessary glyphs, Unicode box-drawing characters such as `┌`, `─`, `┐`, and `│` can be used directly without alternate-character toggles. They are ordinary text, so correct encoding and glyph support are still required; ASCII `+`, `-`, and `|` provide a simple fallback.

The `ALT_CHAR` option in [mangle_display](../set/mangle_display.md) strips the alternate-character attribute. Similar mangling at other input or output stages can remove it before display. Formatting attributes and local terminal mappings are not a promise that another IRC user's terminal will render the same graphics.

## Source references

- `include/irc.h`: `ALT_TOG` is Control-E.
- `source/screen.c`: alternate-character attribute toggling, stripping, and terminal output.
- `source/term.c`: `acs_chars` capability lookup and `term_sgr()`.
- `source/functions.c`: `getcap()`, `chr()`, and `repeat()`.
- `source/if.c`: `fec` character-pair iteration.
- `source/vars.c`: display-mangling configuration.
