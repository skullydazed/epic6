# set dcc_dequote_filenames

## Removed setting

The former setting used this syntax:

```text
/set dcc_dequote_filenames [on|off|toggle]
```

When on, it attempted to accommodate incoming DCC SEND offers that enclosed filenames containing spaces in double quotes. That compatibility handling could interfere with filenames containing literal double quotes. When off, the former DCC implementation used its strict request parsing instead.

EPIC6 removes DCC file transfers and does not register `dcc_dequote_filenames` as a built-in setting. There is no supported value or filename-parsing mode controlled by it now. It cannot enable receipt of files with spaces or quotes in their names.

Without script-provided handling of unknown settings, an obsolete assignment such as this follows the ordinary unknown-variable path:

```text
/set dcc_dequote_filenames on
```

It reports `No such variable "DCC_DEQUOTE_FILENAMES"`. Remove such assignments from startup scripts. Scripts that accept the same setting name or implement their own transfer mechanism define their own parsing behavior; they do not restore the removed C implementation.

## Scope

This was specifically an incoming DCC offer compatibility switch, not a general rule for quoting filenames in local commands. Current CTCP handling has no built-in DCC SEND negotiation or receiving path to which it could apply. See the removed [dcc get](ctcp_get.md) and [dcc send](ctcp_send.md) commands.

## History

The setting first appeared in EPIC4-1.2.7.

## Source references

- [KNOWNBUGS](../../KNOWNBUGS): records retirement of `/dcc`, `$dccctl()`, and associated functionality.
- [source/vars.c](../../source/vars.c): current registration omits this setting and handles unknown-variable errors.
- [source/commands.c](../../source/commands.c): current built-in command table has no `dcc` handler.
- [source/ctcp.c](../../source/ctcp.c): current CTCP implementation has no DCC SEND filename parser or transfer negotiation handler.
