# Away

For setting or clearing your away message, see the [away command](../commands/away.md):

```text
/away [-one | -all | --] [message]
```

A message requests away status; no message clears it. `-one` is the default and affects the current server context. `-all` updates every existing server record, including stored messages for disconnected entries. `--` allows a message beginning with a dash or slash. For example, `/away Eating dinner` sets the message and `/away` clears it.

Related topics:

- [status_away](../set/status_away.md) controls the status-bar `%A` indicator; it does not send an away request or contain the away reason automatically.
- [Special variables](../special/special_vars.md) documents `$a`, which reads the stored away message for the server context.
- [serverctl](../functions/serverctl.md) exposes per-server stored and confirmed away state.

The stored message and its status indicator are not proof of server acceptance. Away replies, WHO/WHOIS presentation, limits, and delivery to other users depend on the server. See the command page for reconnection behavior and supported options.

## Source references

- `source/commands.c`: away flags and per-server/all-server updates.
- `source/server.c`: stored message, protocol request, and confirmed state.
- `source/status.c` and `source/functions.c`: `%A` and `$a` reads.
