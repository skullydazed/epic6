# window add

## Synopsis

```text
/window [<window>] add <target>[,<target>...]
```

## Description

Add one or more targets to the selected window's target list for output routing, without explicitly selecting a new current query. With no window selector, operate on the current window. If another window owns a target, ownership is transferred to this window.

For ordinary nickname targets, messages associated with that nickname are routed to this window before message-level routing such as `msgs` or `notices`. Explicit output destinations can take precedence. Nickname associations are per server, so adding a nickname does not redirect that nickname's messages on another server. Channel output follows the channel's window association; use [window channel](channel.md) for that purpose.

The argument is one comma-separated token; separate target names with commas rather than spaces. `*` selects the window's current channel, and `.` selects the server's last sent-message destination. Missing values for those shortcuts produce diagnostics such as `You are not on a channel` or `You have not messaged anyone yet`. A comma is the list separator here, so it cannot reliably select the last sender through the usual comma shorthand.

New targets normally produce `Added target to window target list`. Transferring ownership also removes the old window's entry and reports its removal. A target already owned by the selected window is not added twice. There is no check that a nickname currently exists, and this operation sends no IRC request.

Use [window remove](remove.md) to remove targets and [query](../commands/query.md) or [window query](query.md) to select the destination for ordinary input.

## Edge cases

With no target argument, the command prints `ADD: Add nicknames to be redirected to this window`; it does not list current targets. `/window -add` is currently a no-op and does not clear the list.

The implementation resets each added target's query-selection counter to zero. Adding an existing current query can therefore deselect it. A window whose query counter is also zero can recognize a zero-counter list entry as its query, so this operation does not guarantee that the effective query remains unchanged in every case.

Some non-nickname target prefixes (`=`, `%`, `@`, and `/`) are treated globally by the ownership lookup rather than by the ordinary per-server nickname rule. Their routing and sending semantics depend on the relevant target type; adding an entry does not create a connection or process.

## Examples

Route messages associated with Joebob to the current window:

```text
/window add joebob
```

Add two nickname targets to window 3:

```text
/window 3 add alice,bob
```

## Source references

- [source/window.c](../../source/window.c): `windowcmd_add`, `get_target_special`, `add_window_target`, `check_window_target`, and query counter lookup implement parsing, ownership transfer, and query interactions.
- [source/screen.c](../../source/screen.c): output routing checks channel or nickname associations before level-based windows.
