# set show_channel_names

## Synopsis

```text
/set show_channel_names [on|off|toggle]
```

Default: `on`. With no argument, [set](../commands/set.md) displays the current value.

## Description

When on, displays the list of users received while EPIC is synchronizing a channel after joining it. Turning it off suppresses the built-in `Users on <channel>: ...` display for those NAMES replies.

This controls display only. EPIC still processes the server's nickname list for its channel state. [On names](../on/names.md) still runs, and scripts can display their own output regardless of this setting. Numeric hooks can also affect default display before this setting is consulted.

The setting is checked only for NAMES replies belonging to a channel currently being synchronized. Replies outside that state use the ordinary NAMES display and are not hidden by it. Thus you can normally request a list explicitly with [names](../commands/names.md) even when it is off. An explicit request made while synchronization is still in progress can still take the synchronization branch.

## Effects and limitations

The server supplies the names and can split them across several replies; the setting does not combine them into a single list, request extra information, or change server permissions. It changes handling of subsequent replies and does not erase lists already displayed. It is global rather than a per-channel or per-window choice.

## Example

Hide automatic join-time user lists:

```text
/set show_channel_names off
```

After channel synchronization completes, request a list explicitly:

```text
/names #example
```

Restore the automatic display with `/set show_channel_names on`.

## Source references

- [source/vars.c](../../source/vars.c) and [include/config.h](../../include/config.h): boolean registration and on default.
- [source/numbers.c](../../source/numbers.c): numeric `353` updates channel names, runs numeric and names hooks, and checks this setting only in the synchronization display branch.
- [source/names.c](../../source/names.c): `channel_is_syncing` identifies the channel synchronization state.
