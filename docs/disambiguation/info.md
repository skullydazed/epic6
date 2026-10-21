# Info

Do you mean:

- The [info command](../commands/info.md), `/info [server]`, which requests IRC server software information? With no arguments it also displays local client credits. Server replies arrive asynchronously, and their content and availability depend on the server.
- The [info function](../functions/info.md), `$info(selector)`, which returns local client version or compilation information without sending an IRC request? For example, `$info(v)` returns the client's version-identification text.

The command displays information and sends a request; the function supplies a value for expressions and expandos. See the individual pages for accepted arguments, selectors, and error behavior.

## Source references

- `source/commands.c`: `info` command, credits, and protocol request.
- `source/functions.c`: `info()` selectors and returned build information.
