# set mangle_logfiles

## Synopsis

```text
/set mangle_logfiles [<rules>]
/set -mangle_logfiles
```

Default: unset, with no global logfile mangling. With no rules, [set](../commands/set.md) displays the current value. Unsetting the variable clears the global selection.

## Description

Specifies mangling (postprocessing) of output before it is written to a logfile. Display attributes can be inconvenient in a text file; these rules let you remove unwanted formatting before saving it.

Separate rules with commas or spaces. Rules modify the existing selection; prefix a rule with a hyphen to remove it. Names are case-insensitive. Begin with `none` to replace the selection. Unknown names are silently ignored, and the stored value is rewritten as a canonical list of enabled rules.

Supported rules are `normalize` (alias `ansi`), `color`, `bold`, `blink`, `reverse`, `underline`, `alt_char`, `all_off`, `nd_space`, `escape`, `unprintable`, `other`, `all`, and `none`. `-all` clears the selection. See [mangle types](../special/mangle_types.md) and the [mangle_display rule descriptions](mangle_display.md). There is no separately recognized `italic` rule, although `all` includes italic stripping.

`normalize` interprets supported escape and color sequences; by itself it preserves formatting as IRC control codes. `all` combines normalization with formatting stripping, excluding `escape`, `other`, and `unprintable`. `unprintable` alone can remove only the introducing control character of a color or escape sequence, leaving its following text. `other` removes ordinary text and is rarely useful for readable logs.

## Scope and ordering

The global [log](log.md) uses this selection. Per-window logs and selective [log](../commands/log.md) objects also use it when their own mangling mask is empty. A nonempty `/window log_mangle` or selective-log `mangle` selection replaces the global rules for that destination. Clearing an override with `none` restores the global fallback; it does not force unmangled logging while the global selection is nonempty.

The current implementation adds [no_control_log](no_control_log.md)'s unprintable-character stripping only when the logging caller supplies a nonempty log-specific mask. It does not add that rule when falling back to this global selection.

Mangling precedes [log_rewrite](log_rewrite.md) or a log-specific rewrite. Text introduced by rewriting is not mangled again. A nonempty mangling mask invokes the shared normalizer, which also expands tabs and discards Unicode C1 control characters. With no effective mask, the logging routine copies the line without this pass. Log start/end markers are written directly and bypass it.

Changes affect subsequent writes, including to already open logs. They do not alter existing file contents, enable logging, change the live display, or change lastlog records. Window logs are processed before display mangling, so [mangle_display](mangle_display.md) is not a substitute for logfile rules.

## Examples

Replace the global selection with normalization and formatting stripping:

```text
/set mangle_logfiles none,all
```

Strip colors while retaining other formatting:

```text
/set mangle_logfiles none,normalize,color
```

Remove color stripping from the current selection:

```text
/set mangle_logfiles -color
```

Disable global logfile mangling with `/set -mangle_logfiles`; nonempty log-specific selections remain effective.

## History

`set mangle_logfiles` first appeared in epic4pre2.001.

## Source references

- [source/vars.c](../../source/vars.c): unset default, registration, and callback.
- [source/log.c](../../source/log.c): `add_to_log` chooses rules, applies mangling before rewriting, and writes records; start/end markers bypass this routine.
- [source/logfiles.c](../../source/logfiles.c) and [source/window.c](../../source/window.c): log-specific mangling selections.
- [source/output.c](../../source/output.c) and [source/screen.c](../../source/screen.c): global and window logging callers; `parse_mangle` and `new_normalize_string` implement the rules.
