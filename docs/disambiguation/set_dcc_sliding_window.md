# set dcc_sliding_window

## Removed setting

The former setting used this syntax:

```text
/set dcc_sliding_window [<number>]
```

It controlled how many blocks the former DCC SEND implementation could send before waiting for the receiver's acknowledgements. A window of one used a stop-and-wait approach; larger windows could reduce idle time on higher-latency transfers. This concerned application-level DCC acknowledgements, not the operating system's TCP window size.

EPIC6 removes DCC file transfers and does not register `dcc_sliding_window` as a built-in setting. There is no supported value, default, or transfer-throughput tuning behavior attached to this name. Historical advice to increase it, including very large values, does not apply to EPIC6.

Without script-provided handling of unknown settings, an obsolete assignment such as this follows the ordinary unknown-variable path:

```text
/set dcc_sliding_window 2
```

It reports `No such variable "DCC_SLIDING_WINDOW"`. Remove such assignments from startup scripts. A script accepting the same name would define its own behavior rather than restore the former C transfer implementation.

## Scope

This setting did not tune ordinary IRC message delivery or generic TCP connections. There is no direct built-in replacement for its DCC-specific send window. See the removed [dcc send](ctcp_send.md) command for the status of that transfer path.

## Source references

- [KNOWNBUGS](../../KNOWNBUGS): records retirement of `/dcc`, `$dccctl()`, and associated functionality.
- [source/vars.c](../../source/vars.c): current registration omits this setting and handles unknown-variable errors.
- [source/commands.c](../../source/commands.c): current built-in command table has no `dcc` handler.
- [source/ctcp.c](../../source/ctcp.c): current CTCP implementation has no built-in DCC SEND negotiation or transfer handler.
