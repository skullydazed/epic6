# newaway script

## Synopsis

```text
/load newaway
```

## Description

This script suppresses repeated away notifications from server numeric `301`, while displaying changed away messages. It provides the historical show-away-once behavior through script hooks rather than a `show_away_once` setting.

Outside a recognized [whois](../commands/whois.md) response, the first away message for a nickname is displayed as:

```text
nickname is away: message
```

Later replies with the same nickname and message are silent. Both nickname and message are lowercased before comparison, so a change only in capitalization does not produce another notification. When a different message is received, the script displays the original text and remembers the new message.

While a WHOIS response is in progress, the script always displays the away text in this form, without updating that nickname's suppression cache:

```text
away     : message
```

The script recognizes this interval by numeric `311` and clears it on numeric `318`, tracked by the last server to send data. It does not initiate a WHOIS or ask for away information. Replies and their ordering depend on the IRC server. An interleaved unrelated `301` during that interval is treated as WHOIS output too; missing `318` can leave that mode set for later replies from the same server. Concurrent WHOIS responses are not tracked separately by nickname.

## Requirements and limitations

The [builtins](builtins.md) script must already supply `encode` and `lastserver`; `newaway` does not load that dependency itself. No optional platform feature or external program is needed. There are no script-specific settings or user-facing command arguments.

The away-message cache is keyed only by nickname, not server. The same nickname on different connections shares remembered text. No hook clears it when someone returns, quits, changes nickname, or disconnects. Consequently, the same message may remain suppressed during a later away period or after a reconnect. This is notification filtering, not an authoritative record of whether someone is currently away.

The script installs a serial-zero suppressing `301` hook and serial-311 observers for `311` and `318`. It replaces the normal `301` display; other scripts that replace these hooks or suppress their execution can interfere. Other numerics and your own [away](../commands/away.md) command are not changed.

The package is named `newaway`; `/unload newaway` removes its packaged definitions through the normal [unload](../commands/unload.md) machinery. Its explicit cleanup hook also calls a generic `purge` alias, which this script does not define. Do not rely on that hook as a standalone cache-reset command.

## Example

```text
/load newaway
```

After loading with the standard support definitions available, repeated server away replies are filtered automatically. The load itself sends no IRC request and prints no notification; later matching replies cause the output described above. The internal `_na.show` helper returns `1` for changed cached text and `0` for a repeated value, but is not a general away-status query.

## Other notes

The original script was written by Matthew Green and placed in the public domain. The retained source credits Joseph Carter's EPIC4 rewrite in June 1999 and records further changes in July 1999.

## Source references

- [script/newaway](../../script/newaway): cache comparison, numeric hooks, WHOIS formatting, and package declaration.
- [script/builtins](../../script/builtins): `encode` and `lastserver` wrappers.
- [source/numbers.c](../../source/numbers.c): numeric `301` supplies nickname and away text; hook suppression controls its built-in display.
- [source/server.c](../../source/server.c): `serverctl(last_server)` identifies the last server processed.
- [source/hook.c](../../source/hook.c): hook matching, serial numbers, and suppression.
- [source/alias.c](../../source/alias.c): package tagging and unload behavior.
