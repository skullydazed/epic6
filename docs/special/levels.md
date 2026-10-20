# Supported message levels

A level classifies a piece of client output, such as a public message, join notification, or system error. A **level description** selects several categories as a bit mask. Window routing, lastlog retention, logging, and activity notification use level selections for different purposes; selecting levels in one subsystem does not automatically configure the others.

## Level descriptions

Separate names with commas. Names are case-insensitive, and a leading hyphen removes a category from the mask built so far. The common parser also accepts space-separated names, although the surrounding command may accept only one argument; comma-separated descriptions avoid that ambiguity.

| Description | Selection |
| --- | --- |
| `msgs` | Private-message output only. |
| `msgs,publics` | Private and channel messages. |
| `msgs,publics,-publics` | Private-message output only. |
| `all` | Every mask bit, including dynamically added categories. |
| `all,-msgs` | Everything except private-message output. |
| `none` or `-all` | Empty mask. |

Descriptions are processed from left to right; `all` sets the mask and `none` clears it. Names may be abbreviated by prefix, but the first matching registered name wins rather than producing an ambiguity error. Prefer full names, especially in scripts that create extra levels. Unsupported names can be reported and ignored by the caller; the normalization function deliberately ignores parse errors.

A command can add its own leading `+`/`-` semantics around this common description syntax. For example, [window level](../window/level.md) distinguishes replacing its selection from adding or removing categories. Check the specific command before assuming a mask operation is incremental.

## Built-in categories

| Level | Typical output |
| --- | --- |
| `other` | Output not assigned a more specific category; `crap` is an alias for this same bit. |
| `publics` | Channel messages. |
| `msgs` | Private messages between users. |
| `walls` | Messages addressed to a group rather than an ordinary channel or directly to this client. |
| `notices` | Notice messages. |
| `wallops` | Wallops messages. |
| `operwall` | Operwall-style messages recognized in the wallops handling path. |
| `opnotes` | Operator-directed server notices. |
| `snotes` | Other server notices. |
| `actions` | CTCP action messages. |
| `ctcps` | Other CTCP messages. |
| `invites` | Invitations to channels. |
| `joins` | Channel joins. |
| `nicks` | Nickname changes. |
| `topics` | Channel topic output. |
| `parts` | Leaving a channel without leaving IRC. |
| `quits` | Leaving IRC. |
| `kicks` | Channel ejections. |
| `modes` | Mode changes assigned this category by their handler. |
| `syserr` | System-event errors, including networking failures. |
| `user1` through `user10` | Categories available for script-defined output. |

These are classifications assigned by client handlers, not IRC protocol permissions or channel modes. Hooks and scripts may suppress default output or create replacement output with a different level. `all` and `none` are mask selectors rather than additional ordinary categories.

## Levels and servers

Level names and bit numbers belong to one client-wide registry. Window claims are normally distinguished by server: windows on different connections can claim `publics` independently. Claiming a level with `window level` removes that claim from other windows on the same server, including hidden ones.

A level claim is a routing preference, not an exclusive display filter. Explicit window destinations, channel ownership, window nickname targets, and [current_window_level](../set/current_window_level.md) can take priority. If no claim matches, the router has fallback destinations. Output with no server can match a claiming window on any server. See [window level](../window/level.md) for the routing order.

[Lastlog levels](../window/lastlog_level.md), [notify levels](../window/notify_level.md), and [log](../commands/log.md) selections have their own effects. Routing a category to a window does not guarantee it is retained, logged, or marked as activity.

## Creating your own levels

[levelctl](../functions/levelctl.md) creates categories and adds names for existing categories. An alias name shares a bit; it does not create a second independent category. There is no level-deletion operation, and the registry has a fixed capacity, so create names deliberately rather than once per message.

```irc
@ report_level = levelctl(add reports)
/window level +reports
/xecho -level reports A local report
```

The assignment stores the allocated level number, or `-1` if allocation fails. Adding a level alone does not classify incoming IRC events under it; the example explicitly assigns the level to local output with `xecho`. The window command claims it on the current window's server. Creating a category does not send an IRC request or require a script package.

Use `$levelctl(lookup reports)` to retrieve its number and `$levelctl(normalize all,-reports)` to format a mask. Numeric identifiers depend on the runtime registry and should not be hard-coded. Registry operations also accept prefixes, so choose distinct names and verify results; see the function page for alias validation and capacity details.

## Source references

- `source/levels.c` and `include/levels.h`: initial registry, masks, name parsing, capacity, and `levelctl` operations.
- `source/parse.c` and `source/ctcp.c`: assignment of levels to incoming output.
- `source/window.c`: window level claims and output context.
- `source/screen.c`: destination routing and activity notification.
- `source/lastlog.c` and `source/logfiles.c`: retention and logging masks.
- `source/commands.c`: `xecho -level`.
