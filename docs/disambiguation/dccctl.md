# Dccctl: removed DCC control function

The former `dccctl()` function inspected and changed records in the built-in [DCC](dcc.md) subsystem. Its operations enumerated connections, matched types/users/files, queried readiness and held state, selected default ports, and read or wrote attributes with `get` and `set`.

EPIC6 has removed that subsystem and no longer registers `dccctl()`. There is no bundled replacement or current connection-reference namespace for these operations. Historical `refnums`, `held`, `writables`, `default_port`, `want_port`, and `get/set` calls do not provide supported connection management in the current client.

## Former record data

The old records described chat, send, get, raw, and raw-listen connections. They included filenames and peers, requested ports, local/remote addresses, transfer counts, file/resume sizes, timestamps, and internal flags. Those fields and numeric flags are not a current EPIC6 API. Script code containing their names does not create the missing records or restore transfer support.

The retained [connect](connect.md) function name is only a compatibility placeholder, and [listen](../functions/listen.md) likewise does not create the former raw connections. [execctl](../functions/execctl.md) controls a different resource: local subprocess descriptions, not DCC sockets.

Repository notes discuss a possible future return of DCC/direct sockets. That does not establish current support or promise that a future interface will preserve these operations and flags.

## History and source

`dccctl()` first appeared in EPIC4-1.1.11; later revisions added hold state, offer addresses, readiness, and default-port controls.

- `source/functions.c`: current registry lacks `dccctl`; raw-connection compatibility placeholders remain distinct.
- `KNOWNBUGS`: explicit removal of `/dcc`, `dccctl()`, and related facilities.
- `UPDATES`: DCC removal and possible future redesign.
