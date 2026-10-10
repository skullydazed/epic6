# notice

Send a notice to an IRC nickname or channel, or text to another supported destination.

## Synopsis

```text
/notice target[,target...] [text]
/notice -server/target text
/notice %process text
/notice /command arguments
```

## Description

`notice` works like [msg](msg.md), but IRC nickname and channel destinations receive `NOTICE` instead of `PRIVMSG`. It reads one whitespace-delimited target token and uses the rest as message text. Comma-separated destinations are handled individually. Server policy determines delivery; local output does not confirm receipt.

The IRC notice convention avoids automatic replies that could create reply loops. The current built-ins do not enforce that convention for `msg` or `notice`: `msg` is not automatically converted into a notice inside an incoming-message hook, and these two commands do not reject sends inside incoming-notice hooks. Scripts must avoid automatic notice reply loops themselves. The separate [ctcp](ctcp.md) command does refuse use while processing a notice and selects reply mode while processing an incoming private message.

## Destinations and output

The destination syntax is shared with `msg`, including `-server/target`, `%process`, `/command`, sink target `0`, file target `@number`, window logfile `@Wnumber`, generalized logfile `@Lnumber`, local display `@Ewindow`, and raw-command target `"`. See [msg](msg.md) for their requirements and effects. Process and command destinations have the same effect with either built-in. The historical `=nickname` spelling is currently forwarded as an IRC target; this sending path does not route it to DCC chat.

When the entire target token is `*`, it selects the current window's channel; without a channel it discards the text. A token of `.` selects the last recorded IRC destination sent to on that server, and `,` selects the last recorded private-message sender. Missing history produces a diagnostic. These substitutions do not expand individual entries in a destination list or a nested `-server/target` destination.

Outgoing IRC notices to both nicknames and channels use the `send_notice` hook and ordinarily echo `-target-> text` at notice output level. Suppressing that hook suppresses the local echo, not transmission. Nested sends can suppress hooks and display or be refused to limit recursion. See [on](on.md).

Destination [encoding](encoding.md) applies. Long IRC text is split into chunks of approximately 480 encoded bytes; further truncation can occur at the server line limit. Splitting does not guarantee preservation of multibyte characters. Empty text sends nothing except to subprocess input, where it sends a blank line. A missing target prints usage. IRC destinations require a usable connection; local destinations have their own requirements. The built-in is always registered and has no separate options.

## Incoming notices

Incoming notices first pass through CTCP reply processing. CTCP-only notices can be consumed there without reaching ordinary notice hooks; CTCP replies use `ctcp_reply` and any registered reply handlers. They are not treated as new CTCP requests that automatically require a response.

Notices from the connected server, or with no sender prefix, take the server-notice path. Recognized operator-kill text is offered to `kill`, recognized operator-notice text to `oper_notice`, then remaining output to `general_notice` and `server_notice`. A suppressing hook can stop later notification handling. This sender-based path applies even to a channel target.

Other notices pass through `general_notice`, followed by `public_notice` when addressed to a channel you are on, or `notice` otherwise. The built-in ordinary notice path does not apply notice-specific ignore or flood checks, and does not call `encrypted_notice`. CTCP content has its own processing rules. Scripts can provide additional filtering through hooks.

## Examples

```text
/notice buddha hello there
/notice #channel the meeting starts soon
/notice buddha,booya see you later
/notice /echo local output
```

## History and source

`notice` originated in ircII. Registration, `e_privmsg`, shared destinations, splitting, and outgoing hooks are in [source/commands.c](../../source/commands.c). Incoming ordinary and server-notice handling is in [source/parse.c](../../source/parse.c), CTCP replies in [source/ctcp.c](../../source/ctcp.c), and server transmission in [source/server.c](../../source/server.c).
