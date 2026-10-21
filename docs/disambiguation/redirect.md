# Redirect: removed output-forwarding command

The former `/redirect destination command` copied client output associated with the current server to message destinations, subprocesses, or direct chats. A destination of `0` discarded output. Its forwarding could outlast the immediate command while a server request completed, including unrelated output from the same server.

EPIC6 has removed `/redirect` and its associated `on redirect` event. There is no current built-in destination syntax, forwarding interval, cancellation rule, or bundled replacement for that interface. Historical examples such as `/redirect #channel whois nickname` do not establish working forwarding in the current client. The related standalone [flush](flush.md) command is also retired.

## Current interfaces for narrower tasks

- [xecho](../commands/xecho.md) controls where explicitly supplied **local** text is displayed; it does not forward a stream of server output to IRC recipients.
- [msg](../commands/msg.md) sends explicitly supplied text to a destination.
- [exec](../commands/exec.md) provides process input/output and callbacks, with its own routing options.
- [on](../commands/on.md) hooks can handle particular events or replies explicitly; their arguments and suppression behavior depend on the event.

These tools are separate operations, not a drop-in recreation of the old server-wide forwarding window. A script implementing forwarding must choose the specific events and text it intends to send; a request's completion does not make all intervening output part of its reply.

## Source references

- `source/commands.c`: current command registry omits `redirect`; message and local-output commands remain separate.
- `source/hook.c`: current named-event registry omits the former redirect event.
- `source/exec.c`: subprocess routing and callbacks.
- `UPDATES` and `KNOWNBUGS`: command and event retirement.
