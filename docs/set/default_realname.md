# set default_realname

## Synopsis

```text
/set default_realname [<text>]
/set -default_realname
```

With no text, [set](../commands/set.md) displays the current value. Text can contain spaces. `/set -default_realname` unsets the string; it does not rerun startup default discovery.

## Description

Sets the default IRC realname (IRCNAME) used during server registration. This is the descriptive trailing field of the IRC `user` request, not your nickname, username, or account name. Its visibility and acceptance depend on the server.

At startup, EPIC initializes the setting from the first available nonempty source in this order:

1. Environment variable `IRCNAME`.
2. Environment variable `NAME`.
3. Environment variable `REALNAME`.
4. An already configured default realname, if present.
5. The user's system password-entry GECOS field, up to its first comma.
6. The initialized `default_username` setting.

That initialization stores the chosen value in this setting. It is not a fallback search repeated each time a connection is made. In particular, unsetting it later does not automatically restore `IRCNAME` or GECOS. Set the desired text explicitly rather than relying on an unset value to produce a useful registration realname.

## Per-server values and timing

A server-specific `default_realname` set through [serverctl](../functions/serverctl.md) takes precedence over this global setting. During registration EPIC copies the selected server-specific or global value into that connection's realname and sends it in `user`.

Changing this setting sends no update to an already registered connection. It affects later registrations that use the global default, normally after connecting or reconnecting. It does not override an existing per-server default. This setting does not implement a server's optional realname-change command; any such protocol feature is separate and server-dependent.

The former built-in `realname` command is not registered in EPIC6. Use this setting for the global registration default. Script-provided commands with that name can have their own behavior.

The supplied CTCP finger responder also reads `default_realname` as a fallback when `IRCFINGER` is absent; that is script-provided behavior, separate from IRC registration.

## Example

```text
/set default_realname Working on IRC scripts
```

Later registrations without a per-server override use that text. Existing server connections retain the realname they registered with until changed through a separate supported mechanism or re-registered.

## History

This setting first appeared under this name in EPIC5-0.3.5. It was previously named `realname`.

## Source references

- [source/irc.c](../../source/irc.c): startup selects environment, GECOS, and username defaults and stores the result.
- [source/server.c](../../source/server.c): `server_register` selects the per-server override or global default and sends `user`; `serverctl` exposes per-server defaults.
- [source/vars.c](../../source/vars.c): string registration with an initially null value and no update callback.
- [source/commands.c](../../source/commands.c): current built-in command table omits `realname`.
- [script/ctcp](../../script/ctcp): script-provided finger fallback uses the setting.
