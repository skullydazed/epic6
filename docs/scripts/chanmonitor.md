# chanmonitor

## Synopsis

```text
/load chanmonitor
```

## Description

This script creates a window named `chanmonitor`, requests ten lines of display space, and copies selected incoming channel messages and actions into it. Each copied line includes the receiving server's reported name and the message destination. It installs script hooks; it does not join channels, send messages, or ask the server to monitor anything.

For ordinary text, the format is:

```text
[server] <sender:channel> message
```

For actions, it is:

```text
[server] destination * sender action
```

Copies go explicitly to the named window through [xecho](../commands/xecho.md). Normal display in the conversation's own window is not suppressed by these hooks, although other hooks can change or suppress output.

## Which messages appear

The ordinary-message hook listens only to [public_other](../on/public_other.md). In EPIC6 that event normally means a joined channel which is not current in its owning window, regardless of whether that window is visible. Consequently, ordinary messages from a non-current channel in a visible window can appear here, while ordinary messages from a channel current in its own hidden window normally use `public` and are not copied. Messages classified as `public_msg`, including some messages from senders outside the cached channel membership, are also outside this hook.

The [action](../on/action.md) hook is broader. It skips an action only when its destination is current in a window on the receiving server and that channel's window is visible. It therefore copies actions from current channels in hidden windows and non-current channels in either visible or hidden windows. It does not require a channel destination, so incoming private actions are copied too. Outgoing actions use another event and are not included.

The currentness check concerns any window on the receiving server, not just the selected input window. These differences mean `chanmonitor` is not a complete feed of all messages in hidden windows. It sees only events delivered to its hooks; it cannot retrieve missed messages or bypass server channel access rules.

## Window setup and limitations

If a window named `chanmonitor` already exists, loading the script reuses it without applying the setup below. Otherwise it creates a visible [new window](../window/new.md) and requests:

- Ten display lines, a single status line, and the status format `[chanmonitor]-%>`.
- No server association through [noserv](../window/noserv.md); explicit copied output can still reach it from different servers.
- [fixed](../window/fixed.md) on, [swappable](../window/swappable.md) off, and [skip](../window/skip.md) on.
- User-visible window number `100`. If occupied, the windows exchange refnums; the existing window is not reused as the monitor.

It then uses [window last](../window/last.md) to return input selection to the previously selected visible window. The fixed size and skip settings affect ordinary resizing and navigation; the window remains explicitly selectable. Normal screen-space, full-screen mode, and window-operation restrictions apply, so creation or the requested size can fail.

The hooks are installed before window creation. If creation fails, or the named window is later killed or renamed, their `xecho -w chanmonitor` output has no destination and is silently discarded. The script does not recreate the window on a message; loading it again can recreate a missing named window.

The script uses `winnum`, `winvisible`, and `servername` from [builtins](builtins.md), normally supplied by the default startup script. Load `builtins` first if your personal startup configuration does not already provide them. There are no script-specific settings or command arguments.

## History

Written by larne; first included in EPIC5-0.2.1.

## Source references

- [script/chanmonitor](../../script/chanmonitor): hook filters, output formats, and window setup.
- [source/parse.c](../../source/parse.c) and [source/names.c](../../source/names.c): public-message classification and channel currentness.
- [source/ctcp.c](../../source/ctcp.c): channel and private action dispatch.
- [source/functions.c](../../source/functions.c): `iscurchan` and `winchan` server/window lookup behavior.
- [source/commands.c](../../source/commands.c): `xecho -w` silently returns when its destination is missing.
- [source/window.c](../../source/window.c) and [script/builtins](../../script/builtins): window operations and compatibility wrappers.
