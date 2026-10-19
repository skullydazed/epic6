# set old_math_parser

## Removed setting

The former setting used this syntax:

```text
/set old_math_parser [on|off|toggle]
```

Turning it on selected the old math parser. EPIC6 removes that parser and does not register `old_math_parser` as a setting. There is no supported value, default, or compatibility mode attached to this name.

Without script-provided handling of unknown settings, `/set old_math_parser on` reports `No such variable "OLD_MATH_PARSER"`. Remove obsolete assignments from startup scripts. A script accepting the name would define its own behavior rather than restore the removed expression evaluator.

## Current expressions

EPIC6 evaluates expressions through the remaining parser in `source/expr2.c`. Scripts that depended on the old parser's operator behavior must be adapted to the current evaluator; this setting cannot restore those semantics. The former `xdebug old_math_parser` selector is also retired.

[Set floating_point_math](../set/floating_point_math.md) controls fractional result formatting and is not a replacement parser selector. Changing it does not enable the removed parser.

## Source references

- [KNOWNBUGS](../../KNOWNBUGS): explicitly records retirement of the old math parser and its `xdebug` selector.
- [source/vars.c](../../source/vars.c): current registration omits this setting and handles unknown-variable errors.
- [source/expr2.c](../../source/expr2.c): remaining expression evaluator.
- [source/debug.c](../../source/debug.c): current debug option table has no old-parser selector.
