# country

## Synopsis

```text
/load country
/country [<flag>] <nickname|country-name|code|pattern>
/lcountry <country-name-or-pattern>
/lcode <code-or-pattern>
```

## Description

The `country` script looks up country names and codes in a table bundled with the script. It can search in either direction and can use a nickname's reported hostname suffix as the lookup term. This is a script alias, not a C country-detection facility or a live registry query.

With no argument, `/country` displays brief usage instructions. A direct lookup prints a `Looking for ...` heading, one line for every matching table entry, and `DONE !`. Each result has the form `COUNTRY NAME is CODE`, with both fields displayed in uppercase. No match produces the heading and completion line without a separate not-found error. There is no function return value defined for these command aliases.

## Input and flags

Ordinarily the script treats the first input word as a nickname and invokes [userhost](../commands/userhost.md). If that lookup reports an unknown user, it searches the original input against the table. Otherwise it searches the final dot-separated component of the reported hostname. For example, a hostname ending in `.ca` supplies `ca` to the table lookup. Cached userhost information may satisfy the lookup without a server request.

The nickname path requires a registered server connection. Without one, the underlying `userhost` request returns without its callback, so there may be no table output. It also uses [wait](../commands/wait.md) to wait for a server round trip while continuing to process events; a delayed server can delay completion. The available hostname depends on server replies, caching, and host cloaking. Its suffix does not establish a person's physical location.

| Flag | Current behavior |
| --- | --- |
| `-c` | Skip nickname lookup and search the table directly. |
| `-f` | Same direct lookup. |
| `-i` | Same direct lookup; it does not restrict matches to the code column. |
| `-n` | Discard this flag and process the remaining input normally. |
| Any other leading `-...` word | Skip nickname lookup, just like `-c`. |

`lcountry` is an alias for `country -c`; `lcode` is an alias for `country -i`. Both search names and codes. There is no length-based distinction between country names and codes, and the flags no longer select separate lookup algorithms.

## Patterns and limitations

Direct lookup compares the supplied pattern against both fields of every table entry using EPIC wildcard matching. Use `*` for a variable-length match and `?` for a single character. The pattern covers the whole field, so use surrounding `*` when searching for part of a country name. Matching is case-insensitive; uppercase is only the output formatting.

Use a direct flag for patterns and country names, especially names containing spaces. The script also bypasses nickname lookup for an initial argument containing a literal `*`, but explicit flags make the intended path clear. Direct lookup does not require a server connection.

A raw email address or hostname is not parsed by the table lookup. For a known hostname, explicitly search its suffix, such as `/lcode ca`. Numeric IP address components are not converted into a country, and this script performs no DNS resolution or IP geolocation.

The bundled table contains historical names, obsolete entries, misspellings, multiple names for some codes, and generic domain suffixes such as `com`, `net`, and `org`. Results are the stored labels, not a guarantee of a current official name or ISO assignment. Multiple result lines can therefore refer to the same code. Loading the script rebuilds its `country.info` table from the bundled data; it does not download updates.

## Examples

```text
/load country
/country -c Canada
/lcode ca
/country -f *United*
/country Alice
```

The first two lookups both print `CANADA is CA`. The wildcard example lists matching names from the table. The nickname example tries Alice's reported hostname suffix, then falls back to looking up `Alice` only if the userhost callback reports an unknown user.

## Other notes

Country information was compiled by Daniel Cedras. The software was written and substantially revised by Jeremy Nelson.

## Source references

- [script/country](../../script/country): aliases, callback, direct wildcard search, output, and static table.
- [source/who.c](../../source/who.c): `userhostbase` connection requirements, cache use, and `userhost_cmd_returned` callback fields.
- [source/commands.c](../../source/commands.c) and [source/server.c](../../source/server.c): `wait` and server round-trip waiting.
- [source/functions.c](../../source/functions.c) and [source/reg.c](../../source/reg.c): `match` and wildcard matching.
