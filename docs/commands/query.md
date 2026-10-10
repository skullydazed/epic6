# query

Choose the current window's conversation target.

## Synopsis

```text
/query
/query target[,target...]
```

## Description

`query` calls the same handler as [window](window.md) `query`, operating on the current window. It makes the chosen target the destination for ordinary input text and commands such as [me](me.md), taking precedence over the window's current channel. It does not open a new window or send a message by itself.

The first argument is a comma-separated target list. Targets are added from left to right, and the final added target becomes the current query. Earlier query targets remain in the window's target list. Selecting an existing target makes it current again; it does not erase the other targets. Targets are associated with windows for routing, and selecting a target owned by another window transfers its ownership to this window.

With no argument, `query` removes only the current query target. The most recently selected remaining target becomes current; if none remains, ordinary input falls back to the window's current channel. It does not clear the entire target list. Use `/window -query` to end all conversations in the window.

Only the first argument token is used by the standalone command; additional text is not a message and is ignored. The token supports double quoting. There are no options, and the built-in is always registered. Changes are local and do not require a live IRC connection or check whether an IRC nickname exists. IRC messages subsequently sent to a query still require a usable connection and are subject to server policy.

## Targets and output

A target can be a nickname or channel. `%number` or `%name` selects an [exec](exec.md) process destination, and `/command` selects an EPIC command destination. These and the other destination types described by [msg](msg.md) take effect when text is sent. A process need not exist when you set the query, but it must exist before text can be sent to it. The historical `=nickname` spelling is accepted as a target string; the current shared sending path does not give it DCC-chat routing.

Each list entry equal to `*` selects the current channel, with a diagnostic if there is none. An entry of `.` selects the server's last recorded sent-message destination, with a diagnostic if there is no history. Commas delimit list entries, so use explicit nicknames instead of the comma shorthand for a last sender in this command.

Adding or removing a target normally prints `Added target to window target list` or `Removed target from window target list`, and query changes update the status bar. Selecting an already-owned target can change the query without an added-target message. The `switch_query` binding can rotate among targets and raises the `switch_query` [on](on.md) event; this setter does not itself raise that event.

## Examples

Start a private conversation:

```text
/query sweetypie
```

Add two conversations, making `booya` current:

```text
/query sweetypie,booya
```

End the current conversation and return to a remaining target, if any:

```text
/query
```

End every query in this window:

```text
/window -query
```

## Source

Registration and the `query` wrapper are in [source/commands.c](../../source/commands.c). `windowcmd_query`, target ownership, current-query selection, special-target substitution, and rotation are in [source/window.c](../../source/window.c). Ordinary input uses the shared sending path in [source/commands.c](../../source/commands.c).
