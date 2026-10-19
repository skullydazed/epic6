# set mangle_display

## Synopsis

```text
/set mangle_display [<rules>]
/set -mangle_display
```

Default: `normalize`. With no rules, [set](../commands/set.md) displays the current value. Unsetting the variable clears its mangling rules.

## Description

Specifies mangling (postprocessing) of output before it reaches the display. Usually this strips special formatting you do not want to see, such as colors. It also affects normalization of status lines and input prompts.

Separate rules with commas or spaces. Rules modify the existing selection; prefix a rule with `-` to remove it. Names are case-insensitive. Start with `none` when you want to replace the selection rather than add to it. The stored value is rewritten as a canonical list of enabled rules; unknown names are silently ignored.

## Rules

| Rule | Effect |
| --- | --- |
| `normalize` | Interpret supported ANSI escape sequences and IRC color sequences as display attributes; make other control characters suitable for display. `ansi` is an alias. |
| `color` | Suppress foreground and background colors. Keep `normalize` enabled to handle colors supplied through ANSI sequences too. |
| `bold`, `blink`, `reverse`, `underline`, `alt_char` | Suppress the corresponding formatting attribute. |
| `all_off` | Ignore the IRC reset-formatting control character; this can let an existing attribute continue longer. |
| `nd_space` | Remove non-destructive spaces, including those produced by interpreted escape sequences. |
| `escape` | Display an escape character as a reverse-video `[` rather than interpreting its sequence. The remaining sequence text is processed normally. This takes precedence over `normalize`. |
| `unprintable` | Remove control characters, including formatting controls and bells. For color and escape sequences this can remove only the introducing control character, leaving following text; it is not a replacement for `normalize,color`. |
| `other` | Remove ordinary text, tabs, and non-destructive spaces. This is rarely useful for normal display. |
| `all` | Enable normalization and formatting stripping, including italic stripping, but exclude `escape`, `other`, and `unprintable`. |
| `none`, `-all` | Clear all rules. |

Individual rules can be removed with forms such as `-color` or `-normalize`. There is no individual `italic` rule recognized by this setting's parser. See also [mangle types](../special/mangle_types.md).

Even with `none`, output still passes through the display normalizer: IRC attribute controls are processed, tabs are expanded, and Unicode C1 control characters are discarded. Clearing the rules does not create a raw terminal-output mode.

## Effects and examples

Display mangling occurs after window logging and lastlog storage, so stripping colors here does not strip them from those records. Logfiles have a separate [mangle_logfiles](mangle_logfiles.md) setting. Changes affect subsequent normalization; the setting does not itself rebuild existing scrollback. A [window rebuild_scrollback](../window/rebuild_scrollback.md) applies the current display rules when reconstructing it.

Remove colors while retaining normal processing:

```text
/set mangle_display normalize,color
```

Allow colors again while retaining the other enabled rules:

```text
/set mangle_display -color
```

Replace the selection with the default:

```text
/set mangle_display none,normalize
```

## History

`set mangle_display` first appeared in epic5-0.0.7.

## Source references

- [source/vars.c](../../source/vars.c): default, registration, and `set_mangle_display` callback.
- [source/screen.c](../../source/screen.c): `parse_mangle`, `new_normalize_string`, display processing, and scrollback rebuilding.
- [source/status.c](../../source/status.c) and [source/input.c](../../source/input.c): status and input-prompt normalization use the display rules.
