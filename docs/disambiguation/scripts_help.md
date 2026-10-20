# help script

## Status in EPIC6

The former `help` script provided in-client browsing of help files. That old file-browser implementation and its settings were retired. The repository retains a later `script/help.irc`, which defines an HTTP-based `help` alias, but its connection dependency is unavailable in EPIC6. It does not currently retrieve or display help pages.

The retained alias is normally loaded by [global](../scripts/global.md). Loading its definitions does not restore the retired transport or the old file browser. This item concerns the script, not a C built-in help command or an `on help` event.

## Retained interface and actual result

```text
/load help
/help
/help <page>
```

The current alias accepts page text, defaulting to `new_user`; use a single page identifier, since there is no hierarchy or multi-topic parsing. It prints a `Fetching help page ...` message, then calls `connect` with its configured host and port `80`.

The current C `connect()` implementation unconditionally returns an empty string. The script therefore prints `Help: Could not connect to <configured-host>` and returns immediately. It does not open a socket, make a DNS query, install the per-request hooks, or obtain page text. Changing its configured host or starting from a connected IRC window does not change this failure path. There is no success return value or supported alternative transport option in the alias.

## Obsolete settings and implementation

The older file-browser interface included:

```text
/set help_path <path>
/set help_window on|off|toggle
/set help_prompt on|off|toggle
/set help_pager on|off|toggle
```

The current retained script creates none of these settings, and they are not registered as C settings. They do not select a local EPIC6 help directory, window, prompt, or pager. Remove obsolete assignments rather than expecting them to repair `/help`.

The later HTTP wrapper stores ordinary script variables such as `help.server`, `help.baseuri`, and `help.prompt`; these are not replacements for the old settings. Its unreachable receive path expects `dcc_raw` connection/data/close events, rewrites a subset of document markup, collects lines in arrays, and displays them in a visible window after connection close. The required DCC raw event is absent from the current event table. Its pagination call is commented out, so even that retained code does not implement the old pager controls. Supplying a replacement `connect` function alone would not restore this implementation.

Use the documentation pages directly outside this retired in-client browser. There is no working built-in replacement configured by these names.

## History

The older file-browser script was written by howl and first appeared in EPIC4-1.2.5. The retained HTTP-based `help.irc` identifies its author as skullY and is dated July 17, 2009. These are different implementations of the familiar help entry point.

## Source references

- [script/help.irc](../../script/help.irc): retained alias, default page, connection-failure branch, and obsolete receive/paging code.
- [source/functions.c](../../source/functions.c): `function_connect` is a compatibility placeholder returning empty.
- [source/hook.c](../../source/hook.c), [source/vars.c](../../source/vars.c), and [source/commands.c](../../source/commands.c): current event, setting, and command registrations.
- [script/global](../../script/global): default startup load of the help script.
- [KNOWNBUGS](../../KNOWNBUGS): records retirement of pre-web help-file support and DCC functionality.
