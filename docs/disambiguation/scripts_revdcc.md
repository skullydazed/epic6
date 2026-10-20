# revdcc script

## Status in EPIC6

The `revdcc` script formerly allowed a sender behind a firewall to send a file to a recipient who could accept an incoming connection. It reversed the usual DCC connection direction: the recipient listened and the sender connected. Both ends needed the script, and it did not solve the case where neither end could accept incoming connections.

That file-transfer functionality is unavailable in EPIC6. The retained script depends on the removed `dcc raw` and `dcc close` commands and the retired `dcc_raw` and `dcc_lost` events. Its `$connect()` and `$listen()` calls now return empty strings without opening sockets. Loading the script does not restore a listener, outgoing transfer connection, or file-transfer protocol.

## Former interface

```text
/load revdcc
/revsend <nickname> <filename>
/revget <nickname> <filename>
/revclose <nickname> <filename>
/revhelp
```

These aliases remain in the source. `revsend` attempts a userhost lookup, checks local file accessibility, queues the filename, and sends a CTCP `REVDCC SEND` offer. `revget` attempts the now-inert listener call and sends a CTCP `REVDCC GET` reply containing a chosen port. `revclose` sends a CTCP `REVDCC REJECT` reply. `revhelp` prints the historical command help. Those messages or local announcements are not evidence that a transfer can run.

The historical data path used external `uuencode` on the sender, sent encoded lines over a raw DCC socket, and wrote an intermediate `.uu` file on the receiver before invoking `uudecode`. Installing those programs cannot repair the missing client socket transport. There is no current successful transfer example or replacement setting in this script.

## Retained behavior and limitations

Loading defines global aliases and CTCP hooks and prints a version/help banner. It does not automatically transfer a file. The helper `ento` also expects `encode` from [builtins](../scripts/builtins.md), which the script does not load itself.

The reply hooks have an additional compatibility problem: they match an old layout such as `nickname REVDCC GET ...`, while current [ctcp_reply](../on/ctcp_reply.md) supplies nickname, destination, command, and arguments. Their positional assumptions do not match ordinary current replies.

The script's shared variables, pending queues, random port selection, and shell-command construction do not provide modern transfer validation or isolation. It has no transfer timeout or robust filename handling, and its own source notes missing flood protection and receiver-side existence checks. Do not invoke the retained commands expecting a supported transfer service; they can still send IRC messages or attempt local operations even though the required transport is absent.

## History

This script was written by archon and first appeared in EPIC3.001. Its source is dated September 1, 1996 and identifies the retained version as `1.0d.epic`, crediting hop's assistance.

## Source references

- [script/revdcc](../../script/revdcc): retained aliases, CTCP exchange, raw DCC operations, and uuencoding/decoding commands.
- [source/functions.c](../../source/functions.c): `function_connect` and `function_listen` return empty; the former DCC socket code is disabled.
- [source/commands.c](../../source/commands.c): no built-in `dcc` command is registered.
- [source/hook.c](../../source/hook.c): current event table omits `dcc_raw` and `dcc_lost`.
- [source/ctcp.c](../../source/ctcp.c): current generic CTCP reply argument layout.
- [KNOWNBUGS](../../KNOWNBUGS): DCC retirement and removal work.
