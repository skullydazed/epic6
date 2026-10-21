# Version

Do you mean:

- The [version command](../commands/version.md), `/version [server|nickname]`, which displays local client identification when called without arguments and requests server version information? Targets and remote replies depend on the server.
- The obsolete [version function](../functions/version.md), `$version(...)`, which always returns the compatibility string `2.8` and ignores its arguments?

The function does not report the running EPIC version or inspect a server connection. For local client build information, use [info](../functions/info.md), such as `$info(v)`. For cached server information and advertised features, see [serverctl](../functions/serverctl.md).

A [CTCP](../commands/ctcp.md) VERSION request to another client is another distinct operation; `/version nickname` sends the IRC server command, not that CTCP request.

## Source references

- `source/commands.c`: version-command display and protocol request.
- `source/functions.c`: fixed compatibility function and local `info()` fields.
