# nickcomp script

## Synopsis

```text
/load nickcomp
/set nickcomp on|off|toggle
/set nickcomp_char <character>
/set nickcomp_len <number>
/set nickcomp_multi on|off|toggle
```

## Description

This script automatically completes a partially matching nickname when you address someone in a channel. The customary form is a nickname followed by a colon, a space, and your message. With this script loaded, a shortened nickname in that position can be expanded before the message is sent.

Completion happens when you submit the input line, not while typing and not through a Tab binding. The default trigger is a first word ending in `:`, followed by a space and the remaining text. For example, if `example` is a known nickname in the current channel:

```text
exa: hello
```

is submitted as:

```text
example: hello
```

The script uses the current channel's locally cached nickname list on the current server. An exact nickname match takes precedence over longer prefix matches and uses the spelling in that list. Otherwise, matching nicknames beginning with the supplied text are selected. With multiple completion off, only the first matching nickname in the returned channel list is used; with it on, all matches are joined with commas. No match leaves the supplied prefix unchanged. The script does not require a unique match or ask you to resolve ambiguity.

## Settings

| Setting | Default | Meaning |
| --- | --- | --- |
| `nickcomp` | `on` | Install the input hook and completion function; `off` removes them. Supports `toggle`. |
| `nickcomp_char` | `:` | Marker ending the first input word. Use a single ordinary character. |
| `nickcomp_len` | `3` | Minimum prefix length for automatic completion. Shorter prefixes pass through without lookup. |
| `nickcomp_multi` | `off` | Return all prefix matches as a comma-separated list instead of the first. Supports `toggle`. |

These are script-provided settings created using [addset](addset.md), which the script loads itself. All four defaults are explicitly applied when loading the script. The length setting accepts nonnegative integers, including zero.

Although described as a character, `nickcomp_char` is implemented as a string setting. Multi-character values are not a reliable delimiter: hook matching uses the configured text, while extraction through `before` treats it as a set of delimiter characters. Wildcard characters also affect the hook pattern. Empty values are unsuitable. The default colon avoids these complications.

## Effects and limitations

A suppressing [input](../on/input.md) hook replaces matching submitted lines with a reconstructed line passed to [sendline](../commands/sendline.md). `sendline` processes interactive text without invoking that input hook again. The script sends no separate nickname lookup request; message delivery still requires a connection and target and depends on the server. The reconstructed line uses one space after the marker and the remaining input words, so it should not be used to preserve special spacing exactly.

The hook has no explicit channel-only guard. It can also match input submitted in a query or another context; the lookup still uses the current channel value rather than the query recipient. Cached channel state can be incomplete during synchronization. Other scripts replacing the same serial-zero input hook can displace this behavior.

Prefix matching uses the client's wildcard `pattern` function, so wildcard characters in a supplied prefix are patterns rather than escaped literal characters. This is not general completion of commands, filenames, or nicknames elsewhere in a message. It does not rewrite text sent directly by other scripts through commands that bypass `on input`.

While enabled, `$nickcomp(pattern)` returns the completed nickname, comma-separated matches when configured, or the original pattern if unmatched; empty input returns empty. Direct function use does not enforce `nickcomp_len` or append the marker. Turning the setting off removes this alias as well as the hook. No external program or optional platform feature is required.

## Example

```text
/load nickcomp
/set nickcomp_len 2
/set nickcomp_multi on
```

With `example` and `exodus` in the current channel and no exact nickname `ex`, submitting `ex: hello` addresses both matching names in a comma-separated prefix. Use `nickcomp_multi off` to select only the first match.

## History

This script first appeared in EPIC5-0.3.1. The retained source credits BlackJac and is dated June 8, 2006.

## Source references

- [script/nickcomp](../../script/nickcomp): settings, completion function, and input hook.
- [script/addset](../../script/addset): script-provided setting creation.
- [source/functions.c](../../source/functions.c): `onchannel`, `pattern`, `before`, and `unsplit` implement lookup and text construction.
- [source/names.c](../../source/names.c): `create_nick_list` and `is_on_channel` use cached channel membership.
- [source/input.c](../../source/input.c): submitted input dispatch and suppression.
- [source/commands.c](../../source/commands.c): `sendlinecmd` processes the rewritten line.
