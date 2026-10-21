# Window target lists

Each window has a list of targets associated with it, usually nicknames. These associations influence output routing and the window's current query. They do not verify that a nickname exists, send a message, or join a channel.

## Output routing

In normal fullscreen routing, output associated with an ordinary nickname is sent to the window whose target list contains that nickname on the same server, before message-level routing. This is not a promise that every line mentioning the nickname goes there: the output must carry the corresponding routing context. An explicit output window takes precedence, suppressed output is not routed, and non-fullscreen output follows a separate path.

Channel output follows the channel's own window association rather than an arbitrary nickname-list claim. Use [window channel](../window/channel.md) for channel placement. Scripts and commands that explicitly direct output can bypass ordinary target routing. See [levels](levels.md) for level-based routing.

## Current query and send target

Query selection uses per-target recency counters. The current query is the entry matching the window's current query counter. [window query](../window/query.md) explicitly selects a query; [switch_query](../bind/switch_query.md) can cycle query priorities. A window can retain older targets while another is current.

The current query is used in these places:

- `%Q` in the status format, through the `status_query` wrapper, and `$q` in expando evaluation.
- `$windowctl(get <refnum> query_nick)`, window descriptions, and the query column in window listings.
- `$querywin(<target> [server])`, which searches **current queries**, not every entry in every target list. It returns the first matching user-visible refnum, or `-1`; supply the server to avoid an ambiguous cross-network match.
- The effective send target, exposed by `$t`: query first, then current channel.
- [send](../commands/send.md), ordinary interactive text, and [me](../commands/me.md), which use that effective target.
- [say](../commands/say.md), which instead prefers the current channel and uses the query only when there is no channel.
- [exec -out](../commands/exec.md), which captures the effective target when that option is processed; it does not automatically follow later query changes.

Default window-log filename construction uses the current channel first, then the query if no channel exists, then a `Window_<refnum>` fallback. An explicit [window logfile](../window/logfile.md) bypasses that default construction. The query affects a filename, not a hostname.

A server can reject the eventual message or action regardless of local target selection. A query and a current channel remain separate concepts in EPIC6.

## Modifying the list

| Action | Result |
| --- | --- |
| `/window query nick` | Add or select a target as the current query. |
| `/window query` | Remove only the current query; another remaining target can become current. |
| `/window -query` | Remove all target-list entries. |
| `/window add nick` | Add a routing target without explicitly promoting it to current query. |
| `/window remove nick` | Remove that target and recompute the current query from remaining priorities. |

[query](../window/query.md), [add](../window/add.md), and [remove](../window/remove.md) accept comma-separated target lists as one argument. Their argument-free and hyphen-prefixed forms differ; consult the individual pages rather than treating them as interchangeable list operations. Adding a routing-only target uses counter zero and does not provide a general guarantee that the current-query result is unchanged in every counter state.

For example:

```text
/window 2 query alice
/window 2 add bob
/window 2 query
/window 2 -query
```

The first selects Alice in window 2; the second associates Bob for routing. The third removes the current query, and the fourth clears all remaining conversations. These operations do not part channels or erase saved output.

## Server scope and ownership

Ordinary nickname associations are per server. Claiming a target in another window on the same server moves its association out of the previous owner. The same nickname on another server is a different conversation.

Internal target lookup also has legacy global-target exceptions for names beginning with `=`, `%`, `@`, or `/`. These exceptions do not make ordinary nickname output cross-server, and target-list acceptance alone does not establish a working external-process or protocol destination. Prefer the documented destination syntax of the sending command.

## Source references

- `source/window.c`: target ownership, counters, query selection, list operations, getters, and logging filenames.
- `source/screen.c`: output-routing priorities and separate non-fullscreen path.
- `source/commands.c`: query-first/channel-first sending, interactive text, and actions.
- `source/functions.c`: `querywin`, `$q`, and `$t`.
- `source/exec.c`: `-out` target capture.
