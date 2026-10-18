# set dcc_long_pathnames

## Removed setting

The former setting used this syntax:

```text
/set dcc_long_pathnames [on|off|toggle]
```

When on, it displayed full pathnames for files being transferred to or from the client through DCC. When off, it displayed only the filenames, omitting parent directories. It affected presentation only, not the actual filename or transfer destination.

EPIC6 removes DCC file transfers and does not register `dcc_long_pathnames` as a built-in setting. There is no supported value or pathname-display mode controlled by this name now.

Without script-provided handling of unknown settings, an obsolete assignment such as this follows the ordinary unknown-variable path:

```text
/set dcc_long_pathnames on
```

It reports `No such variable "DCC_LONG_PATHNAMES"`. Remove such assignments from startup scripts. A script accepting the same name would define its own display behavior rather than restore the removed DCC implementation.

## Scope

This was a DCC transfer display option, not a general pathname-formatting rule for local commands, logs, or script loading. There is no current built-in DCC transfer list to reformat. See the removed [dcc list](ctcp_list.md), [dcc get](ctcp_get.md), and [dcc send](ctcp_send.md) commands.

## Source references

- [KNOWNBUGS](../../KNOWNBUGS): records retirement of `/dcc`, `$dccctl()`, and associated functionality.
- [source/vars.c](../../source/vars.c): current registration omits the setting and handles unknown-variable errors.
- [source/commands.c](../../source/commands.c): current built-in command table has no `dcc` handler.
- [source/ctcp.c](../../source/ctcp.c): current CTCP implementation has no built-in DCC transfer negotiation handler.
