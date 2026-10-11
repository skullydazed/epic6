# $encodingctl()

## Synopsis

```text
$encodingctl(refnums)
$encodingctl(match <server-refnum> <sender> <receiver>)
$encodingctl(get <refnum> <field>)
$encodingctl(set <refnum> encoding <encoding-name>)
$encodingctl(delete <refnum>)
$encodingctl(create <target> <encoding-name>)
$encodingctl(check <encoding-name>)
```

## Technical

Inspects and changes the same local character-encoding rules used by [`/encoding`](../commands/encoding.md). The rules are kept in memory and do not negotiate an encoding with a server. There are 128 slots, including the built-in `console`, `scripts`, and `irc` rules. Deleted slots can be reused; a reference number is not a permanent rule identity.

Operations and fields are case-insensitive and accept prefixes. Use full spellings to avoid ambiguity: `r` means `refnums`, `s` means `set`, `c` means `check`, and the getter prefix `s` means `server_part`, not `source`. Arguments use normal function word parsing, including double-quoted grouping when `/xdebug dword` is enabled. Reference numbers use EPIC's usual integer conversion. Arguments after those required by an operation are ignored.

## Options and returns

| Operation | Behavior and return |
| --- | --- |
| `refnums` | Return occupied slot numbers in ascending order, as a space-separated list. |
| `match` | Return the **encoding name**, not a rule number, selected for the specified message. Use literal `->` as sender for an outgoing message; otherwise sender is treated as an incoming sender. |
| `get` | Return the selected property of an occupied slot. |
| `set` | Change an occupied rule's encoding if validation returns `0` or `-3`; return that validation result. |
| `delete` | Return `1` after removing an ordinary rule, or `0` for a built-in rule, which cannot be deleted. |
| `create` | Create a rule in the lowest free slot, or update an existing case-insensitive exact target. Return its slot number. Unsupported encoding returns `-1`; no free slot returns an empty string. |
| `check` | Validate an encoding name and return the result listed below. No rule is changed. |

Missing arguments, unrecognized operations or fields, and out-of-range or unoccupied references for `get`, `set`, or `delete` return an empty string. A successful `create` can return `0`, and a successful `set` normally returns `0`; do not interpret these results using simple truthiness. `create` can update built-in rules as well as ordinary ones.

| Getter field | Value |
| --- | --- |
| `target` | Original rule target text. |
| `encoding` | Configured encoding name. |
| `server_part` | Parsed server selector, or empty if absent. |
| `target_part` | Parsed nickname/channel selector, or empty if absent. |
| `server_part_desc` | Empty string; currently unimplemented. |
| `magic` | `1` for a built-in rule, `0` for an ordinary rule. |
| `source` | `1` for a locale-derived value, `2` for a hardcoded fallback, or `3` after a user update. |

Built-in rules have no parsed server/target parts. Only the `encoding` field can be set. Updating closes cached conversion handles and sets the source to `3`, while retaining the rule's target and built-in status.

## Encoding validation

| Result | Meaning |
| --- | --- |
| `0` | Accepted, including the exact spelling `UTF-8`. |
| `-1` | The platform does not support opening a converter from the encoding to itself. |
| `-2` | A converter from the encoding to UTF-8 cannot be opened. |
| `-3` | At least 24 individual byte probes failed as incomplete/invalid input; the encoding is nevertheless accepted by `set` and `create`. |

The probe tries byte values 32 through 254 individually. It does not fully validate multibyte encodings or guarantee conversion in both directions. These function operations run the check without its explanatory warnings. Available encodings depend on platform iconv support.

## Matching rules

`match` requires a numeric server reference, not a server name. For outgoing messages, target rules match the receiver. For incoming messages, they may match the sender or receiver. Target names match exactly and case-insensitively, not as wildcard patterns; server selectors use EPIC's server-description matcher.

Applicable rules have decreasing priority: server/nickname, nickname on any server, server/channel, channel on any server, server-only, then the built-in `irc` fallback for incoming text. Equal-priority rules use the lowest occupied slot. Without an outgoing match, `match` returns an empty string, meaning normal outgoing UTF-8 is used. Literal `*` is an incoming sender, not the outgoing marker.

Rule targets use the parsing described in [`/encoding`](../commands/encoding.md). Prefer explicit forms such as `EFNet/hop`, `EFNet/`, or `/#example` to avoid ambiguity. `console` and `scripts` are special rules for terminal and script conversion, not ordinary IRC targets.

The matching query selects a rule; it does not inspect actual message bytes. Normal reception can leave valid UTF-8 unchanged without applying legacy recoding even when this query names an encoding.

## Examples

```irc
echo Rule slots: $encodingctl(refnums)
echo Console encoding: $encodingctl(get 0 encoding)
@ validation = encodingctl(check ISO-8859-1)
@ rule = encodingctl(create /example_nick ISO-8859-1)
echo Outgoing encoding: $encodingctl(match 0 -> example_nick)
```

The created nickname rule applies across servers unless a higher-priority rule overrides it. Creation or updating returns the rule's slot; `check` returns a validation code.

## Source

Registered in [source/functions.c](../../source/functions.c). Operations, rule parsing, validation, matching, and conversion handles are in [source/recode.c](../../source/recode.c). Server-selector matching is in [source/server.c](../../source/server.c).
