# who command

## Synopsis

```text
/who [options ...] target-or-pattern
/who -line { commands } -end { commands } target
/who -literal server-arguments
```

## Description

`who` queries detailed information about users matching a channel, nickname, or server-supported pattern. A normal reply supplies channel, nickname, status, username, hostname, server, and realname with hop count. Status conventionally indicates here/away, IRC operator, and channel operator/voice status. The server determines which users and channels are visible; invisible users and secret channels are not universally hidden from every requester, especially channel peers or privileged users.

An explicit `*` selects the current window's channel, and errors if there is none. `**` is passed as a pattern rather than selecting that channel. With no arguments, the command reports `No argument specified`. `-oper` without another target supplies `*.*`. For other filters, supply a target explicitly. If several ordinary target arguments occur, the last one wins; they do not request independent lists.

Requests are asynchronous and tracked in a per-server WHO queue. An adjacent request for the same exact target can reuse a previous unanswered request before its first reply arrives, applying its own normal-reply processing. There is no synchronous user-cache shortcut. Server limits, delayed replies, or unexpected end targets can disrupt queue matching.

## Options

Options are case-insensitive and accept either `-` or `/` prefixes. Abbreviations follow specific minimum lengths, not a general ambiguity check; use full names. Unknown options abort the new request. Conflicting normal-reply filters are combined, which can select no users.

| Option | Effect |
| --- | --- |
| `-oper` | Add the server's `o` request flag and retain normal replies with the operator marker. |
| `-lusers` | Retain normal replies without the operator marker. |
| `-chops`, `-nochops` | Retain normal replies with or without an `@` marker in the checked status positions. |
| `-here`, `-away` | Retain normal replies beginning with `H` or `G`, respectively. |
| `-host pattern` | Match the returned host. |
| `-server pattern` | Match the returned server. |
| `-name pattern` | Match the returned username. |
| `-nick pattern` | Match the returned nickname. |
| `-realname pattern` | Match the realname text both with and without its leading hop-count word. |
| `-line { commands }` | Run a callback for each accepted reply instead of normal hooks/display. |
| `-end { commands }` | Run a callback at the matched end reply instead of the normal 315 hook/display. |
| `-u-i` | Add the server-dependent `x` flag, traditionally requesting extended visibility. |
| `-operspy` | Send `OPERSPY WHO` instead of `WHO`, where supported and authorized. |
| `-ux arguments` | Append the remaining arguments as an extended WHO specification. Put the target and any callbacks before this option. |
| `-dx flags target-text` | Send the first following word as server flags and all remaining text as the target. Put other options before it. |
| `-literal arguments` | Send `WHO` with all remaining text, clearing previously collected script callbacks, filters, and extended modes. |
| `-diagnose` | Display the WHO queue and return without making a query. |
| `-flush` | Discard the WHO queue and return; already sent server requests are not canceled. |

Host, server, username, nickname, and realname patterns are wildcard filters applied by the client to ordinary replies. Each also sets the request target to its pattern; a later target or such option replaces that target. Several filters can be combined, but the server receives only the selected target and request flags. Their wildcard matching is case-insensitive. The realname filter's requirement to match both representations means a pattern such as `*Alice*` is more useful than an exact `Alice` pattern.

Extended modes are server-specific and are not automatically selected from network identity. EPIC does not validate their fields or privileges. For numeric 354 extended replies, the callback or numeric hook receives the returned fields as raw joined text; ordinary filters and the WHO hook are bypassed. There is no automatic conversion into the normal callback layout.

## Reply processing

For ordinary numeric 352 replies, accepted data is offered to the WHO hook and then the 352 hook before default display. The display shows channel, nickname, status, `user@host`, and hop-count/realname text. [`channel_name_width`](../settings/channel_name_width.md) controls the displayed channel width. Normal data is supplied to `-line` as:

```text
$0 channel
$1 nickname
$2 status
$3 username
$4 hostname
$5 server
$6 hop count
$7- realname
```

Callbacks run in their own local scope and argument context. `-end` normally receives `$0` as the replying server, `$1` as the end-reply target, and remaining words as server-supplied end text. The usual end numeric is 315. A matched end removes the request; a mismatched target produces a diagnostic and can leave it queued. Some extended-request server errors are handled as a synthetic end, with only server and target arguments.

`-line` and `-end` require brace-delimited bodies. Missing bodies produce diagnostics. There is no built-in file-output option: in particular, `-f` abbreviates **flush**, not a filename filter or logging request.

## Examples

List operators associated with matching servers, subject to server visibility:

```text
/who -oper -server *.foo.com
```

List channel operators returned for a channel:

```text
/who -chops #blah
```

Handle normal replies and their completion in a script:

```text
who -line {echo $1 is $3@$4} -end {echo WHO finished for $1} #blah
```

## Restrictions and source

The built-in is always registered but silently does nothing unless the current server is registered. It has no function return value. Use hooks or callbacks for completion rather than assuming [`wait`](wait.md) drains every queued request. Flushing can cause late replies to be associated with a later query, so use it only when recovering a confused queue. Host disclosure and enhanced visibility remain subject to server policy.

[source/commands.c](../../source/commands.c) registers `who`. [source/who.c](../../source/who.c) implements `whocmd()`, `whobase()`, options, queue matching, filters, callbacks, and 352/354/end processing. [source/numbers.c](../../source/numbers.c) routes replies and relevant errors; [source/alias.c](../../source/alias.c) implements callback scopes.
