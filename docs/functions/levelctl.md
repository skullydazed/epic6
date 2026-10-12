# $levelctl()

## Synopsis

```text
$levelctl(levels)
$levelctl(add <name>)
$levelctl(alias <old-name> <new-name>)
$levelctl(lookup <name-or-number>)
$levelctl(normalize <level-description>)
```

## Description

Inspects and extends EPIC's output-level registry. Levels are used in window, lastlog, and logging masks. Adding a level does not automatically assign IRC events to it.

| Operation | Behavior and return |
| --- | --- |
| `levels` | Space-separated canonical level names in registration order, excluding aliases. The current implementation also omits level zero (`OTHER`). |
| `add <name>` | Returns the first existing case-insensitive prefix match if one exists; otherwise creates a level and returns its number. Returns `-1` when the 63-level allocation limit is reached. |
| `alias <old-name> <new-name>` | Registers another name for the old level and returns its number. If the new name already prefix-matches a registered name, returns that existing number without creating an alias. |
| `lookup <name>` | Returns the first registered case-insensitive prefix match's number, or `-1`. |
| `lookup <number>` | Returns the first registered name for that number, or empty for an unknown number. `-1` returns `NONE`, and `2147483647` returns `ALL`. |
| `normalize <description>` | Parses a level mask and returns its compact textual representation: `ALL`, `NONE`, comma-separated names, or `ALL` with exclusions. |

Operation dispatch compares the first two characters, except `normalize`, which uses the first character. Names and positional operands use function-word parsing; extra positional words are ignored except that `normalize` consumes the entire remainder. Missing required operands or unknown operations return the empty string.

Numeric lookup is selected for decimal-digit text with an optional leading minus sign, then uses base-detecting conversion; leading-zero octal is significant. Registry numbers are session-local identifiers, not masks, and should not be stored as portable configuration.

## Restrictions

Names are stored with the spelling supplied. New names are not validated as single safe mask tokens beyond argument parsing. Use simple names that do not collide by prefix with existing names. There is no delete operation.

`alias` does not validate the old name before registering a new name. An unknown old name can therefore create an alias with number `-1`; check the old lookup first and avoid registering that result. Aliases can affect later prefix matching.

Normalization accepts space- or comma-separated levels, `all`, `none`, `-all`, and `-<level>` exclusions. Unsupported names are silently ignored. Its current renderer skips level zero, so masks involving `OTHER` do not always round-trip faithfully: normalizing only `other` returns an empty string, and `all,-other` can render as `ALL`. Rendering uses a fixed 512-byte buffer, so long custom names or descriptions can be truncated.

## Examples

```text
$levelctl(lookup public)
$levelctl(lookup 0)
$levelctl(normalize public,msgs)
```

The first looks up the `PUBLICS` level; the second returns `OTHER`; the third normally returns `PUBLICS,MSGS`.

`$levelctl(add scripttrace)` creates a custom level unless the name already matches a registered prefix. `$levelctl(alias publics channeltext)` adds a synonym for `PUBLICS`, provided that `channeltext` does not already match another level.

## History

This function first appeared in EPIC5-0.0.8.

## Source

Registered in [source/functions.c](../../source/functions.c) and implemented by `levelctl` and registry/mask helpers in [source/levels.c](../../source/levels.c). Limits are defined in [include/levels.h](../../include/levels.h).
