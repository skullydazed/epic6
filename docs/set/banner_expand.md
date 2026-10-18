# set banner_expand

## Synopsis

```text
/set banner_expand [on|off|toggle]
```

Default: `off`. With no value, [set](../commands/set.md) displays the current value. `toggle` reverses it; other values are rejected as invalid boolean settings.

## Description

When on, the stored [banner](banner.md) is run through expando substitution each time an output path uses it. When off, those paths take the stored banner as literal text. This is useful for including the current clock value, `$Z`, in informational prefixes.

The setting affects banner use by internal informational messages, system-error diagnostics, and the banner helper used by numeric output and [xecho -b](../commands/xecho.md). It does not add a banner to output that does not otherwise use one. When [show_numerics](show_numerics.md) makes the numeric helper choose a numeric prefix, there is no configured banner to expand for that prefix.

Per-use expansion has an empty argument string: `$*` does not contain the message being prefixed. Expandos and function calls use the execution context of the output path, so avoid assuming that a banner is evaluated in the original incoming message's handler context.

Normal command parsing can expand a `set banner` statement before its text is stored. Preserve the dollar sign when assigning a banner that should expand later; otherwise you can accidentally store a single snapshot of the clock. Turning this setting on cannot recover expandos that were already replaced during assignment.

The stored banner remains unchanged by per-use expansion. Changing this setting affects future output without rewriting existing scrollback or sending an IRC request. It has no immediate redraw callback.

## Example

At the interactive command line, double the dollar sign to store a literal `$Z` for later expansion:

```text
/set banner_expand on
/set banner $$Z ***
/xecho -b A timed informational message.
```

The output prefix uses the current value of `$Z` each time, in your configured clock format. Additional quoting may be needed if these commands are themselves embedded in another layer of script expansion.

To return to the ordinary literal prefix:

```text
/set banner ***
/set banner_expand off
```

## Source references

- [source/output.c](../../source/output.c): `vsay` and `vsyserr` conditionally expand the banner with an empty argument string.
- [source/numbers.c](../../source/numbers.c): `banner` conditionally expands the configured prefix unless a numeric prefix takes precedence.
- [source/expr.c](../../source/expr.c): expando handling treats `$$` as a literal dollar sign.
- [source/vars.c](../../source/vars.c) and [include/config.h](../../include/config.h): boolean registration and default off.
