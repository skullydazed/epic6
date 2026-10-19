# set status_umode

## Synopsis

```text
/set status_umode [<text>]
/set -status_umode
```

Default: ` (+%#)`, including a leading space. With no text, [set](../commands/set.md) displays the current value. Unsetting removes the user-mode field's format rather than clearing actual modes.

## Description

Sets the status-bar format used when the window's server has a nonempty cached user-mode string. If the window has no server or no user modes are recorded, the field is empty. These are your server-level user modes, not channel modes; [status_mode](status_mode.md) is separate.

The field is represented by `%#` in [status_format](status_format.md), or a per-window format. The first `%#` inside `status_umode` is replaced with the recorded mode letters before insertion into the outer line. The default wrapper supplies its own `+`. Other status percent fields are not recursively expanded inside the wrapper.

When exactly one server is connected and [show_status_all](show_status_all.md) is off, the field appears only in the input window of each screen. With multiple connected servers, that restriction does not apply; each window uses its own server. Enabling `show_status_all` also permits the field in noncurrent windows in the single-server case.

## Effects and limitations

The formatter copies the mode string into a 20-byte buffer, so at most 19 bytes of mode text are inserted. It reflects EPIC's cached modes rather than issuing a fresh server query. Available modes, their meanings, and which changes are accepted are server-dependent.

Changing this text rebuilds status formats and requests updates. It does not change modes, connect to a server, or send an IRC request. The outer format must contain `%#`; omitting `%#` from this wrapper can show a fixed label while recorded modes are nonempty.

Ordinary command parsing can expand dollar expressions on assignment. [Status_does_expandos](status_does_expandos.md) optionally expands the assembled line later; the shared wrapper helper escapes dollar signs and backslashes in the inserted value for that pass.

## Example

```text
/set status_umode UserMode: +%#
```

For recorded modes `iw`, this supplies `UserMode: +iw` where the outer format contains `%#`, subject to visibility rules. `/set -status_umode` hides the field without changing your user modes.

## Source references

- [source/vars.c](../../source/vars.c) and [include/config.h](../../include/config.h): registration, status rebuild callback, and default wrapper.
- [source/status.c](../../source/status.c): `%#` mapping, wrapper compilation, and `status_umode` visibility, empty-value, and buffer handling.
- [source/server.c](../../source/server.c): `get_server_umode` returns recorded per-server modes.
