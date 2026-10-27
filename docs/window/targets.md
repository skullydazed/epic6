# Window target lists

A window can retain multiple conversation targets, normally nicknames, while selecting one as its current **query**. The list affects output routing; the selected query also supplies the window's default outgoing conversation target. See [window query](query.md), [window add](add.md), and [windowctl](../functions/windowctl.md).

## Output routing

For ordinary nickname targets, output carrying that nickname as its routing context goes to the owning window on the same server, before ordinary message-level routing. This applies to appropriately associated incoming and outgoing conversation output; it does not redirect every line that happens to mention the nickname in its text.

The original said that all output regarding a listed target always goes there. Current routing has higher-priority paths: an explicit output window can take precedence, and channel output follows the channel's own window association rather than a nickname-style target-list claim. Use [window channel](channel.md) to move channel ownership. Non-full-screen output also has a separate simplified path.

Keeping a target in the list does not establish that it is online or that the server will deliver a message. Removing a target changes future routing claims, not stored output, lastlog, or saved files.

## Where the current query is used

The window's effective outgoing target is its current query when one exists, otherwise its current channel. Individual commands can deliberately choose a different priority.

| Interface | Current behavior |
| --- | --- |
| [`$querywin(target [server-refnum])`](../functions/querywin.md) | Return a user-visible window refnum whose **current query** matches the target, or `-1` when none matches. This does not search every older target-list entry. Without a server restriction, the first matching window can belong to any server. |
| `%Q` in a [status format](../set/status_format.md) | Display the query through the configured `STATUS_QUERY` format. |
| `$Q` inline expando | Expand to the current window's query, or empty when there is none. |
| `$T` inline expando | Expand to the effective target: query first, then channel. |
| [Window describe](describe.md) and [window list](list.md) | Include the current query in their window information. |
| [Window log on](log.md) without an explicit per-window logfile | Form a filename from the global logfile base and a suffix, preferring the current channel, then the query, then `Window_<refnum>`. The original's “default hostname” meant filename. The query is not chosen ahead of a current channel here. |
| `$windowctl(GET <window> QUERY_NICK)` | Return the current query. |
| [Send](../commands/send.md) | Send to the query first, falling back to the current channel. |
| [Say](../commands/say.md) | Prefer the current channel; use the query only when no current channel exists. |
| [Me](../commands/me.md) | Send a CTCP ACTION to the query first, otherwise the current channel. |
| Ordinary text typed without an input-line command prefix | Send to the query first, otherwise the current channel. |
| [Exec -out](../commands/exec.md) | Select the effective target, query first, when configuring process-output redirection. The selected target is stored for that redirection rather than automatically following later query changes. |

Log filenames are chosen when opening the log; changing the query does not rename an already open log. A target selection is separate from the sending command's target-type rules, connection requirements, and server restrictions. Some special targets can name a process or client command rather than an IRC nickname; retaining such a target does not create the corresponding process or facility.

## Modify a target list

With no explicit window selector, these operate on the current window:

| Action | Result |
| --- | --- |
| `/window [<window>] query <target>[,<target>...]` | Add targets and select them as queries, processed left to right; normally the last becomes current while earlier conversations remain retained. |
| `/window [<window>] query` | Remove **only the current query**. A remaining target can become current. This corrects the original claim that the bare form clears the whole list. |
| `/window [<window>] -query` | Remove all target-list conversations, leaving no query. |
| `/window [<window>] add <target>[,<target>...]` | Add routing targets without explicitly choosing them as current queries. It resets their query-priority counters to zero, so re-adding an existing query or a zero-counter list can still affect which query is recognized. |
| `/window [<window>] remove <target>[,<target>...]` | Remove those literal target entries. If the current query is removed, recalculate the query from remaining priorities. |

Use commas, not spaces, to separate targets in these operations. Query and ADD resolve shorthands such as `.` for the server's last sent-message target and `*` for the current channel; REMOVE treats its names literally. See the individual command references for missing arguments, shortcuts, and diagnostics.

For example:

```irc
/window 3 query alice,bob
/window 3 query alice
/window 3 query
/window 3 -query
```

The first adds both conversations and normally selects Bob. The second selects Alice while retaining Bob. The third removes Alice, allowing Bob to become current. The last ends all retained conversations in window 3. These operations do not send a message or part any channels.

## Server scope and ownership

Ordinary nickname associations are per server. The same nickname can belong to different windows on different IRC servers; a nickname claim on one server does not capture the other server's output. Claiming an ordinary target already owned by another window on the same server removes the old entry and moves the claim to the new window.

The implementation has special global ownership lookup rules for target prefixes `=`, `%`, `@`, and `/`, so the ordinary per-server ownership statement is not universal. Those legacy/special prefixes also have target-specific sending rules; for example, DCC facilities associated with historical `=` targets have been removed from EPIC6. See [window add](add.md) and [msg](../commands/msg.md) for the current limits.

The original proposed eventually unifying queries and channels. That was a future design note, not a completed feature: current source still stores channel associations and query target lists separately and applies the distinct priorities above.

## History and source validation

The original revision identifier was `window_targets.txt,v 1.1 2009/06/01 00:47:41 jnelson Exp`.

- [source/window.c](../../source/window.c): target ownership, query counters, current/effective target getters, QUERY/ADD/REMOVE registration and behavior, QUERY_NICK, window descriptions, and log filename construction.
- [source/screen.c](../../source/screen.c): explicit-destination priority, channel routing, per-server nickname claims, and level-based fallbacks.
- [source/functions.c](../../source/functions.c): QUERYWIN/WINDOWCTL registration, current-query lookup, and `$Q`/`$T` expando implementation.
- [source/status.c](../../source/status.c): `%Q` registration and STATUS_QUERY formatting.
- [source/commands.c](../../source/commands.c): SEND/SAY/ME registration, default-target selection, and ordinary interactive input.
- [source/exec.c](../../source/exec.c): EXEC -OUT stores the selected target for process redirection.
