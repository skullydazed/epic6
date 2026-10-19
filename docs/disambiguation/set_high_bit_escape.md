# set high_bit_escape

## Removed setting

The former setting used this syntax:

```text
/set high_bit_escape [0|1|2]
```

It controlled the historical treatment of input bytes with their high bit set:

| Former value | Former behavior |
| --- | --- |
| `0` | Accept the byte without converting it to an escape-prefixed key, subject to the former eight-bit input controls. |
| `1` | Strip the high bit and interpret the resulting seven-bit character as if preceded by Escape. |
| `2` | Choose between those behaviors using the terminal's historical `km` capability. |

EPIC6 does not register `high_bit_escape` as a built-in setting and has no active consumer for these modes. There is no supported value or automatic `km`-based high-bit conversion controlled by this name. The former default of `2` is not an EPIC6 input policy.

Without script-provided handling of unknown settings, an obsolete assignment such as this follows the ordinary unknown-variable path:

```text
/set high_bit_escape 0
```

It reports `No such variable "HIGH_BIT_ESCAPE"`. Remove such assignments from startup scripts. A script accepting the same name would provide its own behavior, not restore the former C input switch.

## Current input handling

Current console input is decoded through the configured console encoding into Unicode code points before entering the key-binding system. For a terminal actually sending UTF-8, use:

```text
/encoding console UTF-8
```

Choose the encoding that matches the terminal. This is distinct from interpreting a Meta key as an Escape prefix; configure the terminal's Meta/Alt behavior and [bind](../commands/bind.md) sequences to match the bytes it actually sends. Changing `TERM` or retaining an obsolete `high_bit_escape` value is not a replacement for correct encoding and key-sequence configuration.

## Source references

- [source/vars.c](../../source/vars.c): current registration omits `high_bit_escape` and handles unknown-variable errors.
- [source/screen.c](../../source/screen.c): console byte handling uses `find_recoding("console", ...)` and `iconv`, then passes decoded code points to `user_input_codepoint`.
- [source/keys.c](../../source/keys.c): current key-binding dispatch handles input sequences without the removed setting.
