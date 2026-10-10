# encoding command

## Syntax

```text
/encoding
/encoding <rule>
/encoding <rule> none
/encoding <rule> <encoding-name>
```

## Overview

EPIC uses UTF-8 internally and normally sends IRC messages in UTF-8. Valid incoming UTF-8 is accepted without applying a legacy-encoding conversion. If you receive non-UTF-8 text, or need to send another encoding, use `encoding` rules to tell EPIC how to convert it. See [encodings and locales](../encodings_and_locales.md) for console configuration.

Rules are local client settings, not requests to negotiate an encoding with the server or recipient. The command is available in every build and needs no server connection to list or change rules. Available encodings depend on the platform's iconv conversion support.

## Syntax forms

With no arguments, the command lists all rules, including their numeric slots, encoding names, and origins. With one argument, it displays the rule having that exact rule text, compared case-insensitively; this is not a query for the rule that would win for a message. An absent rule produces no matching entry.

With `none`, it deletes that rule. An absent rule reports `There is no encoding for ...`; built-in fallback rules cannot be removed.

With an encoding name, it creates or updates the exact rule and reports its new encoding. Rule and encoding are whitespace-delimited tokens; additional arguments are ignored. There are no options.

Encoding names must be supported by the system and convertible to UTF-8. Unsupported names or missing conversions are rejected. A check also tries individual byte values: encodings with many unconvertible bytes produce a warning but are still accepted. Acceptance is therefore not proof that every character can be converted in both directions. Use the conventional `UTF-8` spelling for UTF-8.

There are 128 rule slots, including the three built-ins. If all slots are occupied, adding another rule reports that there is no more room. Rules are kept in memory; put commands in your startup script to recreate them in a later session.

## Rules

The explicit rule form is `server/target`, where either side can be omitted. A target is a nickname or channel; it is matched case-insensitively as an exact name, not a wildcard. Outgoing rules match the destination. Incoming rules may match the sender nickname or the destination channel.

Use an explicit slash to avoid ambiguity:

- `4` is server reference 4.
- `EFNet/` is a server/group selector with no target.
- `/hop` is nickname `hop` on any server.
- `EFNet/hop` is that nickname on matching servers.
- `/#example` is a channel target on any server.

Without a slash, a number is treated as a server reference, a name containing a dot as a server selector, and other text as a target. In the current parser this also affects bare channel names containing dots; use `/#example.name` rather than `#example.name` to keep it a channel rule. A slash inside a channel name is likewise ambiguous unless the server/target split is explicit.

A server selector uses EPIC's server-description matching: a numeric reference, configured hostname, server-reported name, group, advertised NETWORK name, or alternate name. Name selectors support the server matcher's wildcard rules. A specified nonzero port must match, and other supplied server-description fields can also restrict the match. See [`server`](server.md).

When several applicable rules overlap, highest priority wins:

| Priority | Rule form |
| --- | --- |
| 1 | `server/nickname` |
| 2 | `/nickname` |
| 3 | `server/channel` |
| 4 | `/channel` |
| 5 | `server/` |
| 6 | Built-in `irc` fallback, for incoming non-UTF-8 text only |

Equal-priority matches use the first occupied rule slot encountered, not necessarily the most recently changed rule. With no outgoing match, text stays UTF-8. A nickname rule can override a channel rule for incoming text from that nickname.

## Examples

Server 4 and targets on it without a higher-priority rule use KOI8-R:

```text
/encoding 4 KOI8-R
```

Any server matching group `EFNet` uses CP437 unless another rule wins:

```text
/encoding EFNet/ CP437
```

Use ISO-8859-1 for `hop` generally, but UTF-8 for that nickname on EFNet:

```text
/encoding /hop ISO-8859-1
/encoding EFNet/hop UTF-8
```

Choose different encodings for different ports:

```text
/encoding irc.rusnet.com:6679 KOI8-R
/encoding irc.rusnet.com:6667 UTF-8
```

Inspect and remove the exact nickname rule:

```text
/encoding /hop
/encoding /hop none
```

## Built-in rules

| Rule | Purpose and startup default |
| --- | --- |
| `irc` | Incoming non-UTF-8 IRC text without a more specific rule: ISO-8859-1. It is not the outgoing default. |
| `console` | Console input/output encoding derived from the locale; falls back to ISO-8859-1 when the locale encoding fails validation. |
| `scripts` | Non-UTF-8 script text without a declared encoding: CP437, with ISO-8859-1 used when CP437 is unavailable. |

These rules can be changed but cannot be deleted through this command. Changing them affects broad fallback behavior, so prefer a specific IRC rule when only one server or user needs conversion. Startup requires working conversion support for the console fallback and ISO-8859-1; missing required support can prevent EPIC from starting.

## History

The `encoding` command first appeared in EPIC5-1.1.8.

## Source

See [source/recode.c](../../source/recode.c) for `encoding()`, rule parsing, matching priorities, validation, initialization, and inbound/outbound conversion. [source/server.c](../../source/server.c) implements `serverinfo_matches_servref()`. [source/commands.c](../../source/commands.c) registers the command and uses the scripts encoding when loading files; [configure.ac](../../configure.ac) checks iconv support.
