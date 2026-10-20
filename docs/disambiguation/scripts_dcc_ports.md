# dcc_ports

## Status in EPIC6

The `dcc_ports` script formerly selected listening ports for outgoing [dcc chat](ctcp_chat.md) and [dcc send](ctcp_send.md) offers, as though a port had been supplied with the former command's `-p` option. EPIC6 removes `/dcc` and `$dccctl()`, so the script no longer controls chat or file-transfer ports. Loading its retained file does not restore those facilities.

## Former interface

```text
/load dcc_ports
/set dcc_port_min 1024
/set dcc_port_max 65535
```

The script still loads [addset](../scripts/addset.md), creates the integer settings `dcc_port_min` and `dcc_port_max`, and initializes them to `1024` and `65535` on load. These are script-defined values, not built-in socket settings. They do not change the port used by IRC server connections, window helpers, or arbitrary sockets.

Changing either value regenerates `dcc_ports.picklist`. The retained `dcc_ports.nextport()` alias removes and returns the first number in that list, regenerating it when empty. With the usual minimum below the maximum, the list includes both endpoints and advances by one before wrapping. The underlying `jot` also generates a descending sequence when the first endpoint exceeds the second; the script does not reject reversed ranges or validate endpoints against the TCP port range. Very large intervals also create large in-memory lists.

The former integration registered this alias through `dccctl(default_port ...)`. That control interface is absent in EPIC6, so no transfer subsystem calls the picker. Calling it manually only returns a number: it does not open, reserve, or check a port. The script itself never tests whether a port is free. Changing a setting therefore resets only the stored number sequence, with no next DCC offer to consume it.

Remove obsolete `dcc_ports` loading and range assignments from transfer setup. There is no direct built-in replacement for this DCC port-range facility.

## History

This script first appeared in EPIC5-0.3.4.

## Source references

- [script/dcc_ports](../../script/dcc_ports): retained settings, reset callbacks, numeric picker, and obsolete `dccctl` registration.
- [script/addset](../../script/addset): script-defined integer settings.
- [source/functions.c](../../source/functions.c): current function table omits `dccctl`; `jot` generates inclusive numeric sequences and `shift` removes their first element.
- [source/commands.c](../../source/commands.c): current built-in command table omits `dcc`.
- [KNOWNBUGS](../../KNOWNBUGS): records retirement of `/dcc`, `$dccctl()`, and associated functionality.
