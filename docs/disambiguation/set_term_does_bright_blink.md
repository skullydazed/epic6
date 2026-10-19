# set term_does_bright_blink

## Retained setting without its former effect

```text
/set term_does_bright_blink [on|off|toggle]
```

The setting remains registered as a boolean, with default `off`. With no argument, [set](../commands/set.md) displays its stored value. EPIC6's rendering code does not read that value, so changing it has no effect on blink or bright-background rendering.

Formerly, on declared that the terminal used the blink attribute for bright background colors, while off selected behavior intended for terminals where blink made text blink. That selectable policy is no longer implemented. There is no need to change the stored value to describe your terminal.

## Current rendering

The renderer sends the blink attribute through the terminal's SGR capability. Independently of this setting, when an ANSI background color is in the range 0–7 and blink is active, it requests background color 8–15 instead. Indexed extended colors and RGB backgrounds use their own color paths without that particular shift. Bright ANSI background escape sequences are also represented directly as colors 8–15.

The result still depends on the terminal and its capabilities; setting this boolean cannot make unsupported bright colors or blinking work. Because the renderer can both send blink and request a bright ANSI background, the old on/off description is not an accurate way to select between those effects.

Changing the value does not itself rebuild display text, change terminal capabilities, or send an IRC request. Existing assignments remain syntactically accepted, but should not be relied upon for terminal compatibility. [Mangle_display](../set/mangle_display.md) can suppress blink attributes as part of its separate display-processing rules.

## Example

```text
/set term_does_bright_blink on
```

This stores `on` but does not change the current rendering policy. Turning it off likewise changes only the stored value.

## History

This setting first appeared before EPIC4pre2.003.

## Source references

- [source/vars.c](../../source/vars.c) and [include/config.h](../../include/config.h): retained boolean registration, off default, and no callback.
- [source/screen.c](../../source/screen.c): `term_attribute` sends blink and applies the unconditional ANSI background shift; escape parsing handles bright background colors. The remaining mention of this setting is a comment, not a value lookup.
- [source/term.c](../../source/term.c): `term_sgr` and background-color helpers emit terminal capability sequences.
