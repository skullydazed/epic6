# twiddle

## Synopsis

`$twiddle(<tilde-expansion>)`

## Technical

Expands a leading `~` in the entire argument as a home-directory reference. `~` and `~/path` use EPIC's stored home directory. `~user` and `~user/path` look up the named system account and substitute its home directory. A suffix after the slash is retained.

EPIC initializes its own home directory from the account database, falling back to `HOME` and then `/` if unavailable. Changing the environment later does not update that stored value. Named-user lookup uses `getpwnam_r` on each call.

If the first character is not `~`, the input is copied unchanged, subject to the pathname buffer limit. There is no word parsing: spaces and literal quotation marks are part of the argument. Quoting the whole path prevents expansion because its first character is then a quotation mark.

## Practical

Use this function to expand a home-relative filename before passing it to another operation. It does not check that the file exists, create it, resolve symlinks, remove `..`, expand wildcards, or change the working directory. [`realpath`](realpath.md) provides separate path-resolution behavior.

## Returns and restrictions

The expanded pathname, the unexpanded input if it does not begin with `~`, or empty for an unknown user or account-lookup error. Empty input returns empty. Errors are not distinguished or printed by this function.

Output is bounded by EPIC's `Filename` buffer (`PATH_MAX + 1` bytes) and can be silently truncated. Named-user lookup uses a fixed 16384-byte account-information buffer; lookup errors, including an insufficient buffer, return empty. There are no options, settings, or server requirements.

## Examples

```irc
$twiddle(~)                 Returns EPIC's stored home directory.
$twiddle(~/notes.txt)        Expands the home directory and retains /notes.txt.
$twiddle(~jvs)              Returns the home directory for user jvs, if found.
$twiddle(~nosuchuser)       Returns empty if that user does not exist.
$twiddle(blah)             Returns blah.
```

## Source

Handler: [`source/functions.c`](../../source/functions.c:5343). Expansion: [`expand_twiddle`](../../source/ircaux.c:1819). Stored home initialization: [`source/irc.c`](../../source/irc.c:632). Path buffer: [`include/irc_std.h`](../../include/irc_std.h:206).
