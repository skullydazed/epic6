# set word_break

## Synopsis

```text
/set word_break [<characters>]
/set -word_break
```

Default: a space followed by a tab. With no characters, [set](../commands/set.md) displays the current value. Unsetting falls back to spaces and tabs for line breaking.

## Description

Contains characters EPIC can use as break opportunities when wrapping output into display rows. It is a literal collection of Unicode code points, not a regular expression, range expression, or list of words. For example, a hyphen in the value means the hyphen character itself.

Spaces have a separate wrapping branch and remain break opportunities even if omitted from this setting. Newlines also force breaks independently. Display normalization normally expands tabs into spaces before wrapping, so this is not a way to preserve tab bytes.

For a listed non-space character, the wrapper normally prefers to break after it, keeping it on the preceding row. Near the right edge, or at the end of the text, it can break before that character instead. Spaces are handled as separators and can be skipped at the start of the continuation. If no usable break is available, the wrapper still splits overlong text to fit; this setting does not disable wrapping.

## Effects and interactions

The rules affect subsequent calls to the display wrapper. Changing the setting does not itself rebuild existing scrollback; [window rebuild_scrollback](../window/rebuild_scrollback.md) reconstructs it using current display rules. [Indent](indent.md), per-window indentation, and [continued_line](continued_line.md) also influence where continuation text fits.

This setting describes display wrapping, not input-editing word boundaries, command argument parsing, or the server's message-length limit. It does not modify the original text stored before display wrapping or send an IRC request.

## Example

Add hyphen break opportunities:

```text
/set word_break -
```

The value is a literal hyphen; spaces still break through their independent handling. Restore the built-in space/tab fallback with `/set -word_break`.

## Source references

- [source/vars.c](../../source/vars.c) and [include/config.h](../../include/config.h): string registration, no update callback, and space/tab default.
- [source/screen.c](../../source/screen.c): `prepare_display` decodes the character collection, handles spaces and forced newlines separately, chooses break positions, and applies continuation formatting.
