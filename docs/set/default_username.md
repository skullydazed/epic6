# set default_username

## Synopsis

```text
/set default_username [<text>]
/set -default_username
```

With no text, [set](../commands/set.md) displays the current value. `/set -default_username` unsets the string; it does not repeat startup default discovery. Use a nonempty username without spaces for the IRC registration field.

## Description

Selects the client-supplied username sent when registering with a server. It is the first parameter of the IRC `user` request, separate from your nickname, [default_realname](default_realname.md), and any authenticated account name.

At startup, EPIC initializes it from nonempty `IRCUSER`, otherwise the current user's system password-entry login name, otherwise the literal fallback `default`. The command-line `-z` option overrides that initialized value. Later assignments replace the stored string; the environment is not reread each time you connect.

## Registration and server behavior

The registration routine reads the current global setting when sending `user`. Changing it sends no update to existing registrations; it affects subsequent registrations, normally after connecting or reconnecting. There is no per-server username override in that send path.

The server determines the username other users actually see. It can apply validation, rewriting, or an ident-service result according to its own policy, so the requested text is not guaranteed to become your visible ident. This setting does not configure an ident daemon or authenticate an account.

The setting is stored as an ordinary string without username-specific validation. Spaces or other unsuitable text can produce an invalid registration request. Unsetting it does not automatically recover the login name or `IRCUSER`; set the desired valid text explicitly before registration.

The setting also participates in startup fallback selection for the initial nickname and realname, and in EPIC's locally constructed fallback `user@host`. Changing it later does not automatically rename the current nickname or replace the already initialized realname default. That local fallback address is not proof of the identity reported by a server.

## Example

```text
/set default_username ircuser
```

Subsequent registrations request `ircuser` as the username. Existing connections retain their server-established identity.

## History

This setting first appeared in EPIC5-0.3.5.

## Source references

- [source/irc.c](../../source/irc.c): startup username discovery, `-z` override, and initial nickname and realname fallbacks.
- [source/server.c](../../source/server.c): `server_register` sends the global setting in `user`; `get_my_fallback_userhost` uses it for a local fallback address.
- [source/vars.c](../../source/vars.c): string registration, initially null default, standard unset behavior, and no update callback.
