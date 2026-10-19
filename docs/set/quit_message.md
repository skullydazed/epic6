# set quit_message

## Synopsis

```text
/set quit_message [<text>]
/set -quit_message
```

Default: `ircII %s -- What next?`. With no text, [set](../commands/set.md) displays the current value. Unsetting removes the global default.

## Description

Sets the default quit reason used when you issue [quit](../commands/quit.md) without arguments. An explicit `/quit <text>` takes precedence. The default can also be used by other normal server-closing paths when no more specific reason was supplied.

A server-specific quit message, set through [serverctl](../functions/serverctl.md)'s `quit_message` attribute, takes precedence over this global setting. If neither a supplied reason nor a server-specific or global value exists, the closing routine uses `Leaving`.

The closing routine substitutes the EPIC version string for the first `%s` in the chosen reason. It does not perform a fresh alias or function expansion of the stored text at shutdown. Ordinary command parsing can still expand dollar expressions while assigning the setting.

## Effects and limitations

Changing this setting stores a default; it does not disconnect or send an IRC request. During a normal close of a registered server connection, EPIC sends the selected reason in a `QUIT` request if the resulting text is nonempty, then closes the connection. The server determines what other users see and can alter or truncate the reason. A broken connection cannot guarantee delivery.

Unset is different from supplying an empty server-specific reason: an absent value falls through to defaults, whereas an empty final reason causes the closing routine to omit its `QUIT` request. This setting does not guarantee a quit message for crashes, forced termination, or every connection failure. Other closing operations can supply their own reasons instead.

## Examples

```text
/set quit_message Back later
/quit
```

This uses `Back later` unless a server-specific reason takes precedence. Override the defaults for a particular exit:

```text
/quit Taking a break
```

Restore the fallback behavior with `/set -quit_message`.

## Source references

- [source/vars.c](../../source/vars.c) and [include/config.h](../../include/config.h): string registration and default text.
- [source/commands.c](../../source/commands.c): `e_quit` distinguishes explicit arguments from a default exit.
- [source/irc.c](../../source/irc.c): `irc_exit` passes an explicit reason, or no reason, to server cleanup.
- [source/server.c](../../source/server.c): `servers_close_all`, `server_close`, server-specific fallback, and `serverctl` attributes.
- [source/status.c](../../source/status.c): `convert_sub_format` prepares the first `%s` substitution.
