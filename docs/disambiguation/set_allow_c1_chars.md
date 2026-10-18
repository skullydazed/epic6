# set allow_c1_chars

## Removed setting

The former setting used this syntax:

```text
/set allow_c1_chars [on|off|toggle]
```

It controlled whether C1 characters in the range 128 through 159 could reach the display. EPIC6 removes the setting and unconditionally excludes Unicode code points U+0080 through U+009F from its normal display normalization and output paths. There is no supported `on`, `off`, or `toggle` value for it now.

Without script-provided handling of unknown settings, attempting to set it follows the ordinary unknown-variable path and reports `No such variable "ALLOW_C1_CHARS"`. A script accepting that name does not restore the former C display switch.

## Character encoding

The excluded range is a range of Unicode code points, not a blanket ban on every input byte between 128 and 159. Such bytes can occur within valid UTF-8 representations of other characters. EPIC decodes text into code points before making this display decision.

If older text used those byte values for printable glyphs in another character encoding, configure the appropriate text recoding so those glyphs become their intended Unicode characters. Reintroducing this obsolete setting does not select an encoding or font, and no current built-in setting enables literal C1 display.

## History

The setting first appeared in EPIC4-1.1.2 and was subsequently sunset before EPIC6's current variable table.

## Source references

- [KNOWNBUGS](../../KNOWNBUGS): records the sunset of `allow_c1_chars`.
- [source/vars.c](../../source/vars.c): the current variable table omits the setting; unknown variables follow the `unknown_set` and `set` error-hook path.
- [source/screen.c](../../source/screen.c): `new_normalize_string` discards C1 code points; `output_with_count` also excludes them during output.
- [include/config.h](../../include/config.h): retains an unused historical default macro, which does not register a setting or enable display behavior.
