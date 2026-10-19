# set mangle_inbound

## Synopsis

```text
/set mangle_inbound [<rules>]
/set -mangle_inbound
```

Default: unset, with no inbound mangling. With no rules, [set](../commands/set.md) displays the current value. Unsetting the variable disables this mangling pass.

## Description

Specifies mangling (postprocessing) of data received from the IRC server before the client processes it. This can strip unwanted control characters or special formatting, such as colors. It changes the incoming protocol line, including text subsequently seen by ordinary message hooks and stored in lastlog or logs.

Separate rules with commas or spaces. Rules modify the existing selection; prefix a rule with a hyphen to remove it. Names are case-insensitive. Start with `none` to replace the selection. Unknown names are silently ignored, and the stored value is rewritten as a canonical list of enabled rules.

Supported rules are `normalize` (also called `ansi`), `color`, `bold`, `blink`, `reverse`, `underline`, `alt_char`, `all_off`, `nd_space`, `escape`, `unprintable`, `other`, `all`, and `none`. `-all` also clears the selection. See [mangle types](../special/mangle_types.md) and the [mangle_display rule descriptions](mangle_display.md). There is no separately recognized `italic` rule, although `all` includes italic stripping.

## Processing and limitations

The client converts incoming text to UTF-8 before this pass. [on raw_irc](../on/raw_irc.md) runs before mangling and can suppress processing altogether. Mangling then runs on the entire line before it is split into its sender, command, and arguments. Formatting attributes are returned as IRC control codes rather than terminal escape sequences.

When the selection is empty, this pass is skipped entirely. When any rule is active, the shared normalizer also processes IRC attribute controls, expands tabs, and discards Unicode C1 control characters. This can affect more than the particular formatting rule you selected.

Rules that alter protocol controls or ordinary characters can interfere with parsing. For example, `normalize` converts the control-A delimiters used by CTCP, and `unprintable` removes them; either can prevent CTCP messages, including actions, from being recognized. `other` removes ordinary protocol text. `all` includes normalization and therefore has the same CTCP concern. Stripping inbound data cannot be undone later by changing display settings.

To hide formatting locally while preserving normal message processing and stored text, use [mangle_display](mangle_display.md). Inbound mangling affects subsequent received lines and does not change old records or send an IRC request.

## Examples

Replace the selection with IRC color stripping:

```text
/set mangle_inbound none,color
```

Remove that rule while retaining any others:

```text
/set mangle_inbound -color
```

Disable inbound mangling:

```text
/set -mangle_inbound
```

## History

`set mangle_inbound` first appeared in epic4pre1.401.

## Source references

- [source/vars.c](../../source/vars.c): unset default and `set_mangle_inbound` callback.
- [source/parse.c](../../source/parse.c): `parse_server` runs the raw hook, applies inbound mangling, and then parses the line.
- [source/server.c](../../source/server.c): incoming encoding conversion and parser dispatch.
- [source/screen.c](../../source/screen.c): `parse_mangle` and `new_normalize_string` implement the rules and control-character handling.
