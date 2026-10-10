# version command

## Synopsis

```text
/version [server|nickname]
```

## Description

With no argument, `version` displays the version of the EPIC client you are running, then asks your connected server for the version of its IRC server software.

The local line has this format:

```text
Client: ircII version (Commit Id: number) (Internal Version: identifier)
```

The `ircII` label is part of the output even when the version identifies EPIC. The fields are the client's compiled common version string, numeric commit identifier, and internal version identifier. The commit identifier is not a Git commit hash. These values describe the running binary, not necessarily the current source checkout, and are not chosen by a `set` setting.

If a target is supplied, the local client line is omitted. EPIC sends `VERSION target` to the current server; it uses only the first whitespace-delimited argument, leaves quotation marks literal, and ignores additional arguments. There are no client-side options or target validation.

A server name conventionally queries that server. Some servers also accept a nickname to query the server hosting that nickname, or support server masks. The connected network controls these forms and whether remote requests are permitted. A nickname here does not request that user's client version; use [`ctcp`](ctcp.md) `nickname version` for a CTCP client-version request.

## Output and restrictions

Server replies arrive asynchronously. For a normal numeric 351 reply, EPIC displays `Server name: version comments`. It also accepts a shortened reply containing only the version field and displays `Server version`. Numeric-specific [`on`](on.md) hooks and the general numeric hook can handle or suppress that server output. The server decides the content and may mask its real software version.

The built-in is always registered and has no function return value. Local version output does not require an IRC connection, but the server query does; disconnected use can therefore display local information followed by a connection diagnostic. Unknown targets, unsupported target forms, and server restrictions are reported by the server.

## Examples

```text
/version
/version irc.example.net
```

The first displays local client information and queries the connected server. The second queries the named server without displaying the local line, if your network supports it.

## Source

[source/commands.c](../../source/commands.c) registers `version` and implements `versioncmd()`. [source/irc.c](../../source/irc.c) defines the compiled identifiers. [source/server.c](../../source/server.c) selects the server and sends the request; [source/numbers.c](../../source/numbers.c) implements numeric hooks and 351 formatting.
