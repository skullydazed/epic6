# set dcc_auto_send_rejects

## Removed setting

The former setting used this syntax:

```text
/set dcc_auto_send_rejects [on|off|toggle]
```

It controlled whether closing a pending DCC connection with [dcc close](ctcp_close.md), before either peer opened it, sent a DCC rejection to the remote peer. The rejection indicated that the offer had been revoked. That was an EPIC protocol extension whose usefulness depended on support in the other client.

EPIC6 removes the DCC implementation and does not register `dcc_auto_send_rejects` as a built-in setting. There is no supported value, default, or automatic rejection behavior controlled by this name. Neither setting it on nor retaining an old `/dcc close` command restores that behavior.

Without script-provided handling of unknown settings, this obsolete assignment follows the ordinary unknown-variable path:

```text
/set dcc_auto_send_rejects on
```

It reports `No such variable "DCC_AUTO_SEND_REJECTS"`. Remove such assignments from startup scripts. A script that defines a setting or command with the same name supplies its own behavior rather than enabling the former built-in DCC machinery.

## Current scope

Current CTCP request handling does not provide the removed DCC negotiation or rejection implementation. Ordinary IRC messages and queries are not pending DCC sessions, and ending a query does not send a DCC rejection. There is no direct built-in replacement setting for this feature.

## Source references

- [KNOWNBUGS](../../KNOWNBUGS): records retirement of `/dcc`, `$dccctl()`, and associated functionality.
- [source/vars.c](../../source/vars.c): current registration omits the setting and handles unknown-variable errors.
- [source/commands.c](../../source/commands.c): current built-in command table omits `dcc`.
- [source/ctcp.c](../../source/ctcp.c): current CTCP implementation has no DCC negotiation or rejection handler.
