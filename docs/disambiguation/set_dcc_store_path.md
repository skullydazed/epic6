# set dcc_store_path

## Removed setting

The former setting used this syntax:

```text
/set dcc_store_path [<path>]
```

It selected the default directory for files retrieved through [dcc get](ctcp_get.md). Without a configured path, the former receiver used the client's current directory. The destination needed to be writable for a transfer to save successfully.

EPIC6 removes DCC file transfers and does not register `dcc_store_path` as a built-in setting. There is no current default receive directory, pathname validation, or file-saving behavior controlled by this name.

Without script-provided handling of unknown settings, this obsolete assignment follows the ordinary unknown-variable path:

```text
/set dcc_store_path /path/to/downloads
```

It reports `No such variable "DCC_STORE_PATH"`. Remove such assignments from startup scripts. A script accepting the same name defines its own behavior rather than restoring the former C receiver.

## Scope

This setting was specifically for DCC downloads. It did not change the client's working directory or select destinations for logs, script loading, or arbitrary file-writing commands. There is no direct built-in replacement for a DCC receive directory. Current CTCP handling does not negotiate or receive DCC transfers, regardless of whether the old directory still exists or is writable.

## Source references

- [KNOWNBUGS](../../KNOWNBUGS): records retirement of `/dcc`, `$dccctl()`, and associated functionality.
- [source/vars.c](../../source/vars.c): current registration omits this setting and handles unknown-variable errors.
- [source/commands.c](../../source/commands.c): current built-in command table has no `dcc` handler.
- [source/ctcp.c](../../source/ctcp.c): current CTCP implementation has no built-in DCC receive negotiation handler.
