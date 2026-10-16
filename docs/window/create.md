# window create

## Synopsis

```text
/window create
```

## Description

Create an additional physical screen in a new terminal and place a new EPIC window in it. Under GNU Screen or tmux this normally opens another multiplexer window. Under X, it can launch an xterm-compatible terminal. The new screen belongs to the same EPIC client process and can have its own terminal geometry.

The built-in operation takes no arguments or options. Further words are processed as window operations on the newly created window after success. `/window -create` is a no-op. Creating the screen does not itself send an IRC request.

## Requirements and terminal choice

EPIC must be running in the foreground, and [wserv_path](../set/wserv_path.md) must name an installed, compatible `wserv` helper. The terminal launcher and helper must be executable. The helper connects back to the client over two IPv4 loopback connections, so local networking must permit them. X terminal creation also requires access to the X server named by `DISPLAY`.

The implementation chooses the launcher from the environment:

- With `STY` and `DISPLAY`, it chooses GNU Screen unless [wserv_type](../set/wserv_type.md) is `xterm`. Both `screen` and `tmux` values select GNU Screen in this branch.
- Otherwise, with `TMUX` and `DISPLAY`, it chooses tmux unless `wserv_type` is `xterm`. Both `screen` and `tmux` values select tmux in this branch.
- With `TMUX` alone it chooses tmux; with `STY` alone it chooses GNU Screen.
- Otherwise, with both `DISPLAY` and `TERM`, it chooses an X terminal. Without a matching environment, creation fails.

Thus `wserv_type` chooses between an available multiplexer and X when both environments are present; it does not generally force an arbitrary launcher. Its default is `screen`.

GNU Screen options come from [screen_options](../set/screen_options.md). The tmux launcher uses `tmux_options` followed by `new-window`. For X, the executable is chosen from the `XTERM` environment variable, then [set xterm](../set/xterm.md), then `xterm`; additional arguments come from [xterm_options](../set/xterm_options.md).

## Output and failures

EPIC reports `Opening new screen...`, `Opening new tmux...`, or `Opening new window...`, followed by the local listening port. The launcher starts `wserv`, and EPIC waits for its data and control connections before creating the new window. A failed launch or connection can make the operation wait for a roughly ten-second connection timeout.

Missing helper configuration reports `You need to /SET WSERV_PATH before using /WINDOW CREATE`. An unsupported environment reports `I don't know how to create new windows for this terminal`. Socket, launch, timeout, or helper incompatibility errors can produce further diagnostics, and a failed operation reports `Cannot create new screen!`.

The current implementation is not guarded by the former `WINDOW_CREATE` configuration define. Successful operation still depends on the helper, process-launch facilities, environment, and terminal capabilities described above.

## Example

```text
/window create
```

Use [window delete](delete.md) to close an additional screen while preserving its EPIC windows as invisible windows. [window delete_kill](delete_kill.md) also kills the selected window.

## Source references

- [source/window.c](../../source/window.c): `windowcmd_create`, `windowcmd_delete`, and `windowcmd_delete_kill`.
- [source/screen.c](../../source/screen.c): `create_additional_screen`, launcher selection, helper connection setup, and compatibility checks.
- [source/vars.c](../../source/vars.c) and [include/config.h](../../include/config.h): launcher settings, validation, and defaults.
