# debug_input_line

## Synopsis

```text
/bind <sequence> debug_input_line
```

## Description

Print diagnostic information about the current input line locally. The output includes the input text and its hexadecimal bytes, logical character offsets, display columns and codepoints, markers for the display start, cursor and end of input, and the physical cursor position.

This action takes no arguments; additional binding text is ignored. It does not edit or send the input line. It has no default key assignment.

## Example

```text
/bind ^[D debug_input_line
```

Press Escape followed by uppercase `D` to inspect the input line.

See [bind](../commands/bind.md) for sequence syntax.

## Source references

- [source/keys.c](../../source/keys.c): registration in `init_binds`.
- [source/input.c](../../source/input.c): `debug_input_line`.
