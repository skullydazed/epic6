# window scratch

## Synopsis

```text
/window [<window>] scratch [on|off|toggle]
```

## Description

Accept the legacy `scratch` operation without changing window behavior. The boolean is parsed into a temporary value initialized to off on every invocation; no setting is retained.

A valid value reports `Window SCRATCH is ON` or `Window SCRATCH is OFF`. `toggle` always reports on because each call starts from off. No argument reports off and stops subsequent operations in the same command. An invalid value reports that ON, OFF, or TOGGLE is required and also stops the chain.

`/window -scratch` does nothing. For normal output scrolling, see [scroll_lines](scroll_lines.md); for manual history movement, see [scroll_backward](scroll_backward.md) and [scroll_forward](scroll_forward.md).

## Examples

```text
/window scratch on
```

## Source references

`windowcmd_scratch` and `get_boolean` in [source/window.c](../../source/window.c).
