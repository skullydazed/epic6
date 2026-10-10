# msg

Send text to an IRC conversation, an EPIC subprocess, or another supported destination.

## Synopsis

```text
/msg target[,target...] [text]
/msg -server/target text
/msg %process text
/msg /command arguments
```

## Description

`msg` reads one whitespace-delimited target token and treats all remaining text as the message. Commas inside that token separate destinations, which can mix the types below. Each IRC destination is sent separately rather than combined into one IRC recipient list. Empty list entries are skipped.

For IRC nicknames and channels, the built-in sends `PRIVMSG`. Server modes, channel membership, recipient restrictions, and rate limits decide whether it is delivered. A local echo is not delivery confirmation. Normal command expansion can occur before the handler receives the text.

## Targets

| Target | Effect |
| --- | --- |
| `nickname` or `channel` | Send an IRC private or channel message. |
| `-server/target` | Send through another configured server selected by its server description, such as a reference number, name, or group. Unknown descriptions produce a diagnostic. |
| `%number` or `%name` | Send a line to an already launched [exec](exec.md) process, using its EPIC reference or logical name. A newline is appended. |
| `/command` | Execute that EPIC command with the message text as its arguments. |
| `0` | Discard the message. |
| `@number` | Write to an open file handle. |
| `@Wnumber` | Write directly to that window's active logfile. |
| `@Lnumber` | Write directly to that generalized [log](log.md) logfile. |
| `@Ewindow` | Display the text locally using [xecho](xecho.md) with the indicated window target. |
| `"` | Send the message text as a raw server command. |

File destinations use original UTF-8 text rather than destination recoding. They require a valid writable handle or active logfile; write failures are not converted into a `msg` result value. Slash-command destinations run through ordinary command parsing, with an empty argument context for their enclosing expandos. These local destinations have different validation and effects from IRC messages.

The `=nickname` notation does not have a DCC branch in the current sending path; it is forwarded as an IRC target rather than routed to a DCC chat.

### Magic recipients

Three substitutions apply only when the entire target token is exactly the indicated symbol:

- `,` uses the last recorded private-message sender on the current server. If none is recorded, EPIC prints a diagnostic.
- `.` uses the last recorded IRC destination sent to on that server. It can be a channel as well as a nickname. If none is recorded, EPIC prints a diagnostic.
- `*` uses the current window's channel, not its query target. Without a current channel, it becomes sink target `0` and sends nothing.

These substitutions do not expand individual entries in a comma-separated list or the nested target in `-server/target`. Use an explicit destination in those forms.

## Output, encoding, and limits

When display is enabled, outgoing private messages pass through `send_msg`, channel messages through `send_public`, and ordinary local echoes show the target and original text. Suppressing these hooks suppresses the echo, not transmission. Nested sends suppress outgoing hooks and display to reduce recursion; deeper recursive sends can be refused. See [on](on.md).

Text is recoded for each IRC destination using [encoding](encoding.md) rules. Long messages are split into chunks of approximately 480 encoded bytes, preferring whitespace within the preceding 24 bytes. This is byte-based splitting, not a guarantee of preserving multibyte characters. The server sending layer can further truncate a chunk to its IRC line limit, particularly with long destinations. Do not rely on lossless transmission of arbitrary long text.

An empty body is permitted for process input, but sends nothing to other destinations. A missing target prints usage. The handler records the supplied message body; the IRC sending branch also updates the selected server's last-sent destination after attempting a nonempty send, including channel destinations and failed delivery attempts.

Unlike the separate [ctcp](ctcp.md) command, this built-in does not automatically switch to `NOTICE` inside incoming-message hooks or refuse sending inside notice hooks. Use [notice](notice.md) explicitly when that reply behavior is wanted.

## Restrictions and examples

The built-in is always registered and has no separate option parser; `-server/target` is destination syntax. IRC targets require a usable connection. The sending layer normally reports a closed connection subject to the `disconnect` hook; with no server record, no request is sent. Local destination types can work without an IRC connection.

```text
/msg buddha hi there!
/msg #channel how is everyone today?
/msg buddha,#foobar see you later!
/msg -0/booya hello on server zero
/msg %mail x
/msg /echo local output
```

The process example requires an existing launched process named `mail`. Server zero must be a configured connected server for its example to deliver.

## Source

Registration, `e_privmsg`, destination branches, hooks, splitting, and saved-target updates are in [source/commands.c](../../source/commands.c). Process input is in [source/exec.c](../../source/exec.c), file destinations in [source/files.c](../../source/files.c), recoding in [source/recode.c](../../source/recode.c), and server lookup and wire limits in [source/server.c](../../source/server.c).
