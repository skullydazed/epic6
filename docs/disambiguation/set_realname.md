# set realname

## Renamed setting

The former setting used this syntax:

```text
/set realname [<text>]
```

It was renamed to [set default_realname](../set/default_realname.md) in EPIC5-0.3.5. EPIC6 does not register `realname` as a C setting. Use the current name to configure the global realname sent during later IRC registrations:

```text
/set default_realname Example User
```

There is no separate built-in value or default for `realname`. See `default_realname` for startup default selection, per-server overrides, and registration timing. Changing it does not update an already registered connection's realname.

## Script compatibility

The supplied [script/builtins](../../script/builtins) retains an `on set` compatibility handler for `realname`. It forwards the supplied text to `set default_realname` rather than creating a separate variable. That script also defines `realname` and `ircname` command aliases that route through this compatibility handler.

With that handler loaded, `/set realname Example User` therefore changes `default_realname`. Without script handling, the obsolete name follows the unknown-variable path and reports `No such variable "REALNAME"`.

Use the canonical name for queries and unsetting too. In particular, `/set -realname` is not a reliable way to unset the replacement: the C set hook receives the literal marker `<unset>`, which this shim forwards as text. To remove the actual global value, use:

```text
/set -default_realname
```

Unsetting does not rerun startup default discovery. The compatibility handler does not provide a server's optional live realname-change command.

## History

The former setting first appeared in ircII-2.3, replacing the older `realname` command introduced in “plus 3”. Its later rename made the registration-default role explicit.

## Source references

- [source/vars.c](../../source/vars.c): registers `default_realname`, not `realname`; offers assignments to `on set` and uses `<unset>` for unset hook arguments.
- [script/builtins](../../script/builtins): compatibility forwarding and `realname`/`ircname` aliases.
- [source/server.c](../../source/server.c): global and per-server realname defaults used during registration.
