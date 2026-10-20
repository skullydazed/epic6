# netsplit script

## Synopsis

```text
/load netsplit
```

## Description

When a netsplit occurs, an IRC server commonly sends quit messages for people on the disconnected side, followed by join messages when they return. This can cause a lot of scrolling. The `netsplit` script reduces that output by tracking apparent splits and displaying a timestamped `Netsplit` message and a subsequent `Netjoined` message instead of individual departures and returns.

Detection depends on the server's quit text. The script's [channel_signoff](../on/channel_signoff.md) hook matches a channel, nickname, and two apparent server names containing periods. It rejects names matching its bogus-pattern list or containing consecutive periods. This is a heuristic, not a verified report of network topology: ordinary quit text can resemble a split, and other server formats may not be recognized.

For an accepted split, the script remembers each channel and nickname and groups departures by the ordered pair of server names. It prints one `Netsplit at HH:MM:SS (servers)` line while that pair's split flag is set. The first remembered person to rejoin clears that flag and prints `Netjoined at HH:MM:SS (servers)`; other remembered returns are silent. This announces the first observed return, not proof that everyone has returned. A further departure for that pair can start another announcement cycle.

Unrecognized signoffs retain normal handling. A candidate rejected by the bogus checks gets the script's replacement signoff line, potentially once per shared channel. The script replaces all [join](../on/join.md) output: unremembered joins receive its own ordinary join line. It does not change server membership or suppress server mode changes.

## Commands

These aliases become available after loading:

| Command | Effect |
| --- | --- |
| `/boguspat <patterns...>` | Append wildcard patterns identifying names that should not count as split servers. Prints the supplied patterns followed by `added`. |
| `/boguslist` | Print the current patterns. Initially no user patterns are installed. |
| `/bogusclear` | Clear the user patterns; the consecutive-period check remains. |
| `/wholeft` | List remembered channel/nickname pairs with their split server names. Empty tracking produces no lines. |
| `/netclean` | Remove split records whose last recorded departure was more than 300 seconds ago. |
| `/netpurge` | Clear all split tracking immediately, without unloading the hooks or clearing bogus patterns. |
| `/showsplit <variable>` | Recursively display a tracking variable and its descendants, for example `signoffs`. |

The script also defines internal aliases and the helper functions `$currtime()` and `$lformat(width text)`. The former returns local time as `HH:MM:SS`; the latter pads short text on the right without truncating longer text. Loading can replace aliases with these names, including the generic `purge` helper.

## Cleanup and limitations

A serial-70 [timer](../on/timer.md) hook runs `netclean` on clock events, ordinarily once per observed minute. Expiration is measured from the latest recorded departure for a server pair. There is no guaranteed ten-minute cleanup schedule: disabled clock updates or a stalled client can delay cleanup, and entries expire only when the elapsed time exceeds five minutes. A returning person whose record has expired is displayed as an ordinary join.

Tracking is shared across all server connections and does not include a server reference number. Identical channel/nickname combinations or server pairs on different connections can interfere. Channels are lowercased for keys, while nicknames are encoded without first being lowercased; a change in nickname spelling or case can prevent a return from matching its saved record.

The announcements explicitly use the `opnotes` output level. Their visibility and destination depend on window level routing. Other hooks, especially replacements for the same serial-zero join or channel-signoff hooks, can change or displace this behavior. Suppressing a channel signoff also prevents the later ordinary signoff hook for that quit.

The `encode` and `decode` helpers from [builtins](builtins.md) must already be loaded; `netsplit` does not load that dependency itself. No additional package or optional platform feature is required. This is an optional script, not a built-in netsplit detector, and loading it does not create a server-side request. There are no script-specific settings or command return values.

## Examples

```text
/load netsplit
/boguspat *bonk* *thud*
/wholeft
/netpurge
```

`boguspat` applies to the apparent server names in quit reasons, not to nicknames or channels.

## History

This script first appeared in ircII. The retained source identifies itself as version VII and credits Daemon, poxaV, mycroft, and other contributors; its dated modification note is July 3, 1993.

## Source references

- [script/netsplit](../../script/netsplit): detection, tracking, output, aliases, and cleanup.
- [source/parse.c](../../source/parse.c): `p_quit` and `p_join` supply hook arguments and implement default output suppression.
- [script/builtins](../../script/builtins): `encode` and `decode` wrappers used for tracking keys.
- [source/functions.c](../../source/functions.c): `userhost` supplies the current message's prefix userhost for replacement join output.
- [source/clock.c](../../source/clock.c): clock updates dispatch timer hooks.
- [source/hook.c](../../source/hook.c): hook matching, serial numbers, and suppression.
- [source/levels.c](../../source/levels.c): `opnotes` output level.
