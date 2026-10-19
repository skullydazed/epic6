# autoquery

## Synopsis

```text
/load autoquery
/set autocreate_split_queries [on|off|toggle]
```

## Description

After loading, this script automatically creates a query window when someone sends you an ordinary private [msg](../commands/msg.md). It installs an [on msg](../on/msg.md) hook and defines the `_autoquery` helper alias; this behavior is supplied by the script, not enabled by a C built-in setting.

The window name encodes the receiving server's reference number and the sender's lowercase nickname. If no window has that name, the script creates one, associates it with the receiving server, and makes the sender its [query](../window/query.md). Setting a query routes that conversation to the window and makes the sender its default conversation target; it does not send a reply.

If the named window already exists, the script restores its query only when no window on that server currently has this sender as its effective query. It does not otherwise show or select the existing window. Conversely, if the generated name does not exist, the script creates a window even if you already query the sender in a differently named window; claiming the target can move its association to the new window. Nickname changes and changed server refnums can therefore lead to additional windows rather than reuse.

This is not a permanent record of first contact. Killing or renaming the generated window can cause another one to be created on a later message. The script does not automatically close windows when conversations end.

## Window setting

`autocreate_split_queries` is a script-defined boolean, set to `off` whenever the script loads. It requires [addset](addset.md), normally loaded by the default `builtins` startup script; load `addset` first if that support is absent. Changing the setting affects future window creation, not existing query windows.

With `off`, the script creates a [hidden window](../window/new_hide.md), temporarily swaps it into view to configure it, and swaps back. When the swaps succeed, the new conversation remains hidden and the previous window remains visible.

With `on`, the script creates a visible [split window](../window/new.md), but still ends with `window swap last`. If a hidden window exists, that operation swaps it into the new split's position and hides the query window. If none exists, it reports that there are no invisible windows and leaves the new split visible. Thus `on` does not reliably leave the new query visible.

Normal window restrictions apply: splits need screen space, swaps require swappable windows, and additional windows are unavailable in non-fullscreen mode. The script does not recover from creation or swap errors; unsuccessful operations can prevent the intended layout or configuration.

## Message scope

Only incoming ordinary private messages reaching `on msg` trigger creation. Notices, channel messages, outgoing messages, and CTCP-only messages do not. A suppressing `general_privmsg` hook prevents this hook from running. The script does not replace the normal message display; other hooks can still suppress or alter it.

## Example

```text
/load autoquery
/set autocreate_split_queries off
```

An incoming private message from Alice creates her server-specific query window if its generated name is absent, normally leaving it hidden. Use ordinary window selection commands to view it and reply.

## History

This script first appeared in EPIC5-0.3.1.

## Source references

- [script/autoquery](../../script/autoquery): hook, helper, generated names, setting initialization, and both creation paths.
- [source/parse.c](../../source/parse.c): private-message classification and hook order.
- [source/functions.c](../../source/functions.c): `querywin` checks effective queries, with the supplied server restriction.
- [source/window.c](../../source/window.c): query assignment, window creation restrictions, and `window swap last` behavior.
- [script/builtins](../../script/builtins) and [script/addset](../../script/addset): script-defined setting support.
