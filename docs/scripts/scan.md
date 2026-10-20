# scan script

## Synopsis

```text
/load scan
/scan [<channel>]
```

## Description

The `scan` alias displays the users on a channel in six columns. It prints a `Users on <channel>:` heading, then rows containing up to six nickname fields. Each field is formatted to width 11, including status prefixes; longer entries are truncated for display rather than widening the column. Normal terminal wrapping can split a row on a narrow window.

A channel operator is prefixed with `@`, a half-operator with `%`, and a voiced user with `+`. Operator takes precedence over half-operator, while voice can appear alongside either, such as `@+nickname`. The script removes the underlying channel-list placeholders `.` and `?`, so an unknown voice status is not shown explicitly. These are the cached status fields supported by this formatter, not a general rendering of every server-advertised membership prefix.

With no channel argument, the current channel is used. An argument not recognized as channel syntax also falls back to the current channel. Recognition checks the current server's `CHANTYPES` advertisement when available, otherwise the client's standard channel prefixes; it does not check that you have joined the channel. There are no flags, nickname patterns, or server selectors.

## Data and limitations

The script reads local channel membership through `$channel()`, not a fresh server query. The list concerns the current server connection and can be incomplete during channel synchronization or lack status information not yet received from the server. It does not fetch users from a channel you have not joined.

If there is no usable channel argument or current channel, it silently returns. If a syntactically valid channel has no cached record or no cached nicknames, the heading is printed without user rows. No special error is reported for an unknown channel. The order follows the cached nickname list; the script does not perform an additional sort.

Output is local only and has no function-return contract. There are no script-specific settings, additional script dependencies, external programs, or optional platform requirements. Loading only defines the alias; use `/scan` to display a list.

## Examples

```text
/load scan
/scan
/scan #example
```

The first scan uses the current channel. The second displays cached users for `#example` on the current connection.

## History

This script was written by whitefang and first appeared in EPIC4pre0.027. The retained source also carries a 2000 note from tjbh.

## Source references

- [script/scan](../../script/scan): channel fallback, heading, placeholder stripping, and six-column layout.
- [source/functions.c](../../source/functions.c): `function_channel` calls `scan_channel`; `function_ischannel` checks channel syntax.
- [source/names.c](../../source/names.c): `scan_channel` enumerates cached members with operator, half-operator, and voice markers.
- [source/parse.c](../../source/parse.c): `is_channel` uses the server's channel-prefix advertisement or standard defaults.
- [source/expr.c](../../source/expr.c): field-width expansion pads and truncates displayed entries.
