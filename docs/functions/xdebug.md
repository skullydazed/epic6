# xdebug

Return enabled/disabled states of matching xdebug flags.

## Synopsis

```irc
$xdebug()
$xdebug(<pattern> [pattern ...])
```

## Description

The function returns space-separated flag names, each prefixed with `+` when enabled or `-` when disabled. Names are returned in uppercase, in the option table's order. With no pattern it uses `*` to match all reportable names. Supplied patterns are ordinary whitespace-delimited words, without double-quoted grouping, and use EPIC's case-insensitive wildcard matching. Multiple patterns are processed in order; overlapping patterns can produce duplicate entries.

This is a read-only query of the global xdebug mask. It does not enable flags, invoke diagnostics, or change parsing. The [xdebug command](../commands/xdebug.md) changes the mask and describes the available flags. Zero-bit compatibility names such as `memory`, `no_color`, `regex`, `bold_helper`, `new_math`, and `autokey` are omitted from this function's result. Registered flags without a current consumer can still be reported.

## Practical

The signed entries can be passed to the command to restore selected named flags. However, unfiltered output also includes `ALL` as its last entry. `+ALL` means that **any** bit is enabled, not that all bits are enabled. Replaying that entry enables every bit; `-ALL` clears every bit. Therefore the default result is not a faithful snapshot for restoring a partially enabled mask. Remove the `+ALL` or `-ALL` word before replaying a saved result, or query only the individual flags you need. This still cannot represent bits without individual option names.

The command also supports a trailing brace block that restores the original complete mask after execution; use that form for temporary changes when appropriate.

## Returns

The signed states of matching nonzero-bit options, or an empty string if no reportable option matches. No IRC connection is required.

## Examples

```irc
$xdebug(dword)        /* +DWORD or -DWORD */
$xdebug(inbound outbound) /* Two signed states, in the requested pattern order. */
$xdebug(no_color)     /* Empty: this compatibility option has no bit. */
$xdebug(dword extractw) /* Save only these two individual flags. */
```

## History

This function first appeared in EPIC4-1.1.6.

## Source

Registered in [source/functions.c](../../source/functions.c). `function_xdebug`, its option table, and `xdebugcmd` are in [source/debug.c](../../source/debug.c); flag definitions are in [include/debug.h](../../include/debug.h). Wildcard matching is implemented in [source/reg.c](../../source/reg.c).
