# set dcc_use_gateway_addr

## Removed setting

The former setting used this syntax:

```text
/set dcc_use_gateway_addr [on|off|toggle]
```

It selected the address advertised in DCC requests. When on, the former implementation used the IP address associated with the userhost reported by the IRC server; when off, it used the local IP address. The gateway-address option was intended for some NAT or IP-masquerading environments.

EPIC6 removes DCC and does not register `dcc_use_gateway_addr` as a built-in setting. There is no supported value or advertised DCC address controlled by this name now. Turning it on cannot enable a DCC transfer or configure NAT traversal.

Without script-provided handling of unknown settings, this obsolete assignment follows the ordinary unknown-variable path:

```text
/set dcc_use_gateway_addr on
```

It reports `No such variable "DCC_USE_GATEWAY_ADDR"`. Remove such assignments from startup scripts. A script accepting the same name would supply its own behavior, not restore the former C DCC machinery.

## Scope

This was a DCC offer-address choice, not a general switch for the client's IRC connection address or an automatic firewall or port-forwarding configuration. Current CTCP handling has no built-in DCC negotiation path that uses it. See the removed [dcc chat](ctcp_chat.md) and [dcc send](ctcp_send.md) commands.

## Source references

- [KNOWNBUGS](../../KNOWNBUGS): records retirement of DCC and the userhost-discovery feature previously used for DCC.
- [source/vars.c](../../source/vars.c): current registration omits this setting and handles unknown-variable errors.
- [source/commands.c](../../source/commands.c): current built-in command table has no `dcc` handler.
- [source/ctcp.c](../../source/ctcp.c): current CTCP implementation has no built-in DCC offer or negotiation handler.
