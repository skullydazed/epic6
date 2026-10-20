# ircuser

## Synopsis

```irc
/ircuser [username]
```

`ircuser` is a compatibility alias supplied by the [builtins](../scripts/builtins.md) script. It forwards its arguments to the C built-in command `//set default_username`. If your configuration does not load that script, use `/set default_username username` directly or load `builtins` first.

With no argument, the alias displays the current setting. With text, it replaces the client-supplied username used for subsequent server registrations. It has no independent options, validation, or return value beyond the underlying [set](../commands/set.md) behavior.

## About usernames, registration, and identd

When connecting to an IRC server, EPIC sends registration information including a username, nickname, and realname. The username is the first parameter of the IRC `user` request. It is separate from the nickname you choose with `/nick` and from an authenticated IRC account name.

The server determines the identity other users ultimately see. Depending on its policy, it may validate or rewrite the requested username, query an ident service, or mark an unverified username, for example with a leading `~`. Such behavior is server-dependent, not a promise made by this alias. Changing `ircuser` does not configure an ident daemon or perform account authentication, and an ident response is not a general guarantee of a person's identity.

## Description

Changing this value sends no update to existing registrations. The registration routine reads the current global [default_username](../set/default_username.md) setting when registering a connection; a later connection or reconnection is normally needed before the new request reaches a server. The visible username still depends on that server's policy. This is not a per-server username override.

At startup, the setting is initialized from nonempty `IRCUSER` in the process environment, otherwise the operating-system login name, otherwise the literal fallback `default`. The command-line `-z username` option overrides that initialized value. An `/ircuser` assignment changes the stored setting; it does not change the environment or cause startup discovery to run again.

The underlying setting accepts an ordinary string without username-specific validation. Use a nonempty value without spaces; unsuitable text can produce an invalid registration request. Passing several words does not define several usernames. To unset the setting, use `/set -default_username` directly; `/ircuser -` instead assigns a literal hyphen. Unsetting does not restore the startup value automatically.

## Example

```irc
/load builtins
/ircuser chatuser
```

Subsequent registrations request `chatuser`. The equivalent built-in operation is:

```irc
/set default_username chatuser
```

Use the setting before connecting when the first registration must use it. See [command line](command_line.md) and [environment variables](environment_variables.md) for startup configuration.

## Source references

- `script/builtins`: `ircuser` forwards to `//set default_username`.
- `source/vars.c`: string setting registration and ordinary set/unset handling.
- `source/irc.c`: `IRCUSER`, login-name fallback, and `-z` processing.
- `source/server.c`: registration sends the current global username in `user`.
