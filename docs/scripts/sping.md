# sping script

## Synopsis

```text
/load sping
/set sping_timeout <seconds>
/sping [<server>]
/vping [<server>]
```

## Description

This script provides two ways to measure the time between sending an IRC server request and receiving its reply. These are protocol requests routed through your current IRC connection, not operating-system ICMP pings or CTCP pings to another client.

`sping` sends `PING <server> <server>` and measures until a matching [pong](../on/pong.md) sender is observed. `vping` sends `VERSION <server>` and measures until numeric `351` (version information) or a matching `263` reply for `VERSION` arrives. A refusal or rate-limit response can therefore count as a VPING reply; it does not prove the requested operation succeeded.

If the server argument is omitted, the connected server's recorded name is used. There are no command-specific flags. Server routing policy, permissions, reply naming, and restrictions on remote requests determine whether a usable reply arrives. Unsupported or filtered requests may time out.

The script prints a sent-request message, then a measured reply or timeout message. Elapsed time is formatted in seconds with six fractional digits, or minutes plus seconds for longer intervals. This is client-observed round-trip request time, including processing delays, not an isolated measurement of server-to-server lag or six-digit accuracy.

## Timeout and dependencies

The script-provided integer setting `sping_timeout` defaults to **1200 seconds** (20 minutes), explicitly reapplied on load. It accepts nonnegative values; zero schedules an immediate timeout. A setting callback also copies the value into the ordinary `sping_timeout` variable used to create timers. Changing it affects future requests, not existing request deadlines.

The script loads [addset](addset.md) itself. It also expects helpers from [builtins](builtins.md), including `servernum`, `servername`, `myservers`, and `winserv`, to be available. It needs a working IRC connection but no external ping utility or optional client platform feature.

## Matching and limitations

SPING stores a global queue of requests and matches the first saved target whose name equals the PONG sender. The stored originating connection is used for some cleanup, but not for reply matching. Requests to the same target on different connections can interfere, and repeated same-target requests are not distinguished by an echoed unique token. An unrelated PONG from that sender can consume a request. Unmatched PONGs produce a generic received-PONG message instead.

VPING stores requests per source-server refnum and declines a second outstanding request to the same target on that connection. Its temporary `351` and `263` hooks match reply sender text, so aliases, wildcard target names, changed server names, or other scripts' matching hooks can affect results. The hooks suppress ordinary matching reply display; if no saved request matches, they print the reply arguments instead. They are removed after a matching hook invocation, without restoring any prior hook with the same pattern.

A VPING timeout looks up the target with `servernum(target)` rather than retaining the originating connection in its timeout arguments. For a remote target absent from the local server list, or a target associated with another entry, it can look in the wrong pending list and fail to clean up or report the request. Timeout handlers also do not remove VPING's temporary numeric hooks.

The serial-105 connect, server-lost, and `402` hooks attempt cleanup. In the connect/server-lost handlers, the assignment intended to clear `vpings` is local rather than an explicit update of the global queue, so it should not be relied upon to clear pending VPING state. SPING cleanup removes matching source-connection records, and `402` attempts to remove requests for the nonexistent server.

The timing calculation subtracts local seconds/microseconds timestamps. Clock changes and event-loop delays affect the result. The script does not authenticate replies, retry timed-out requests, or provide a function returning measured latency.

## Output placement and state

Output is directed through the `serv_win` helper, which chooses the first numeric window from 1 through 254 associated with the current server. This is not necessarily the initiating window. If no such window exists, that helper returns empty and the script has no reliable fallback destination for its `xecho -w` calls.

Loading runs `clear_pings`, resetting the request lists and `ping_number`. That helper does not cancel already scheduled request timers or remove temporary reply hooks, so reloading or calling it while probes are pending can leave stale callbacks and reused timer IDs. Avoid overlapping this script's `pingN` timer names with other scripts. Its global aliases and serial-zero PONG hook can also replace existing definitions; this is not an independent probe session manager.

## Example

```text
/load sping
/set sping_timeout 30
/sping
/vping
```

This measures replies from the current server using each request type, with a 30-second timeout for new requests. Use an explicit remote server name only if your network permits those requests, and interpret a timeout as lack of a usable matched reply rather than proof that the server is offline.

## History

This script was written by nsx and first appeared in EPIC4-1.2.2. Its source identifies serial 105 for its numbered hooks.

## Source references

- [script/sping](../../script/sping): request queues, timing arithmetic, timeout handling, reply hooks, and output-window search.
- [script/addset](../../script/addset) and [script/builtins](../../script/builtins): setting creation and server/window helpers.
- [source/parse.c](../../source/parse.c): `p_pong` validates replies and handles internal wait tokens before the PONG hook.
- [source/numbers.c](../../source/numbers.c): numeric reply hook arguments and default handling.
- [source/timer.c](../../source/timer.c): scheduling, timer IDs, and callback context.
- [source/server.c](../../source/server.c): server lookup and server-lost hook dispatch.
