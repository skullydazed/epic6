# Server list format in EPIC

EPIC keeps an in-memory list of configured IRC servers. Entries describe possible connections; being present in the list does not mean a server is connected. Each entry has a nonnegative refnum starting at `0`, used to associate connections, windows, channels, and other client state.

## Description format

The basic form is:

```text
server:port:password:nickname
```

Only the destination host is needed for a new destination; later fields can be omitted. Colons remain necessary when skipping an earlier field to supply a later one:

```text
irc.example.net:6668
irc.example.net:::YourNick
```

Additional fields and named fields are supported. Prefer names when skipping several fields:

```text
irc.example.net:PORT=6697:TYPE=IRC-SSL:NICK=YourNick:GROUP=worknet
```

These hosts are placeholders, not connection recommendations. Once a named field begins, every following field must also be named. See [server descriptions](server_description.md) for all fields, defaults, quoting, and duplicate-key behavior.

## Sources at startup

The current startup sequence is:

1. Add server descriptions supplied as command-line arguments.
2. Add descriptions from the whitespace-separated `IRCSERVER` environment variable.
3. If the list is still empty, or the command-line `-a` option requests appending, import the file named by `IRC_SERVERS_FILE`.

These sources do not follow a simple “last source replaces the whole list” rule. Insertions can find and update an existing matching entry rather than create duplicates. Loaded startup scripts can subsequently change the list.

There is no built-in fallback server list or hardcoded default server filename in the current importer. Without `IRC_SERVERS_FILE`, the file-import stage has no file to read. A failed file import does not create a default network connection.

## Server-list files

`IRC_SERVERS_FILE` supplies a path, with normal filename expansion. The importer reads one description per physical line, not a whitespace-separated collection on each line. Use UTF-8 text, no leading indentation, and a newline after every line, including the last one. Invalid UTF-8 lines are skipped; the current reader removes the final byte of each input chunk as if it were a newline, so an unterminated final line can lose data. Keep individual lines short rather than relying on arbitrary-length line handling.

A line beginning with `#` is a comment. Empty lines are skipped. A line beginning with `[` sets the default group for following entries; this group is applied only when the entry does not supply a group field.

```text
# Example server-list file
[worknet]
irc.example.net:PORT=6697:TYPE=IRC-SSL
irc.backup.example.net:PORT=6697:TYPE=IRC-SSL
```

IPv6 literals must be bracketed inside colon-separated fields. In a list file, avoid starting the whole line with `[` because that is recognized as a group heading; use an explicit host field:

```text
HOST=[2001:db8::1234]:PORT=6697:TYPE=IRC-SSL:PROTO=tcp6
```

Replace the example address with a usable endpoint. See [IPv6](ipv6.md).

## Environment and command line

For a shell such as bash, an environment list can be supplied as:

```sh
export IRCSERVER='irc.example.net:PORT=6697:TYPE=IRC-SSL irc.backup.example.net:PORT=6697:TYPE=IRC-SSL'
```

A command-line example, using your installed executable's name, is:

```sh
epic6 -n YourNick irc.example.net:PORT=6697:TYPE=IRC-SSL
```

The first unflagged argument without a dot is treated as a nickname, even if `-n` was already supplied. Be careful with bare numeric refnums or dotless hostnames in that position. See [command line](command_line.md) and [environment variables](environment_variables.md) for the full startup rules.

## Managing the running list

`/server` displays the list. `/server -add description` adds or updates configuration without requesting a connection; `/server description-or-refnum` selects or creates an entry and requests a connection. `/server -update refnum description` updates a closed entry, and `/server -delete refnum` removes a closed entry. The client rejects deletion of its last remaining entry during normal operation.

Deleting an entry leaves a gap; it does not renumber other entries. A new entry can reuse that gap, so refnums are not permanent identities. Disconnecting is different from deletion and ordinarily leaves configuration in the list. A shared lookup can match groups, alternate names, and patterns as well as hosts; consult [referring to servers](referring_to_servers.md) before broad updates or deletions.

Changes to this in-memory list do not automatically rewrite the source file or environment. Keep the intended descriptions in your chosen startup configuration for later sessions. Network access, TLS, authentication, and registration success still depend on the destination and its policies.

## Source references

- `source/irc.c`: command-line nickname handling and startup source ordering.
- `source/server.c`: file import, group headings, insertion/update matching, refnums, display, and deletion restrictions.
- `source/ircaux.c`: description fields and filename normalization.
