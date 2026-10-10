# oper

Request IRC-operator status on the current server.

## Synopsis

```text
/oper name password
```

## Description

`oper` sends `OPER name password` to the IRC server. The supplied name and password must match an operator account configured by the server administrator. The name need not be your current nickname; account naming, permitted connection hosts, authentication requirements, and granted privileges are server-dependent.

Both arguments are required. If either is missing, EPIC prints `Usage: OPER nickname password` and sends nothing. The built-in does not default to your current nickname or prompt for a password. It takes the first two whitespace-delimited words and ignores additional text; it does not provide special quoting for credentials containing spaces. There are no options, and the command is always registered.

The request uses the current server context, falling back to the primary server when no current context exists, and needs a usable connection. Normal outgoing server encoding, line limits, and the `send_to_server` hook apply. See [encoding](encoding.md) and [on](on.md).

## Replies and restrictions

Authentication is asynchronous. The command does not grant privileges locally or produce its own success message. Server replies, commonly numeric `381` on success or errors such as `464`, are displayed through normal numeric handling and hooks. EPIC tracks operator status from incoming user modes `o` or `O`; the success numeric alone does not set that status.

IRC-operator status is distinct from channel-operator status. Servers normally require operator authentication rather than allowing users to grant themselves operator status with [mode](mode.md). [deop](deop.md) requests removal of your own `o` user mode; the server decides the resulting privileges.

Credentials typed directly on the command line are visible there and can be exposed by input history, scripts, outgoing hooks, or debugging. The built-in supplies no hidden-entry interface. A script can use [input](input.md) with `-noecho` to hide typed response characters, but that option controls display only and does not protect credentials from other script or logging behavior.

## Example

```text
/oper operator-account password
```

Replace both placeholders with the credentials supplied by the server administrator.

## Source

Registration and the `oper` parser are in [source/commands.c](../../source/commands.c). Sending and operator-status lookup are in [source/server.c](../../source/server.c), incoming user-mode updates in [source/parse.c](../../source/parse.c), and numeric reply handling in [source/numbers.c](../../source/numbers.c).
