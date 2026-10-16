# window killswap

## Synopsis

```text
/window [<window>] killswap
```

## Description

Attempt to replace the selected window with the most recently hidden window, then destroy the original window. With no window selector, use the current window. The replacement is the first entry in the global invisible-window list, not necessarily a window on the same server. This combines the usual effects of [window swap](swap.md) and [window kill](kill.md) in one operation.

On a successful swap, the replacement takes the original window's screen position and total space, adjusting its output height for its own status bars and toplines. If the original was the screen's input window or EPIC's current window, the replacement takes that role. The original window's scrollback, lastlog, query/nick list, pending channel associations, and private settings are then deleted.

Channels are not explicitly parted; EPIC attempts to move them to another window on the same server. Normal `window_beforekill` and `window_kill` notifications occur during deletion. If the server loses its last associated window, normal server checking closes the connection when its `autoclose` property is on, as it is by default. Saved log files are not erased.

## Failure behavior

**This operation is not atomic in the current implementation. A failed swap does not guarantee that the original window survives.** When a hidden window exists, EPIC attempts deletion after calling the swap helper without checking whether the swap succeeded. Depending on the ordinary kill restrictions, this can delete the original without the requested replacement, or perform another replacement attempt during deletion.

A window with [window killable](killable.md) off is protected: EPIC reports `You cannot KILLSWAP an unkillable window` and stops processing subsequent window operations. If there are no invisible windows, it reports `There are no invisible windows!` and does not delete the target.

The initial swap requires a visible target and both windows to have [window swappable](swappable.md) on. It reports `Window <name-or-number> is not swappable` when either flag prevents swapping, or `You can only SWAP an invisible window with a visible window.` when the target is already hidden. These swap errors do not stop the subsequent deletion attempt. In particular, selecting a hidden, killable target can delete it despite the swap error.

## Arguments and output

There are no arguments or options specific to this operation. Additional words are processed as subsequent window operations using EPIC's resulting current window. `/window -killswap` is a no-op. Successful execution normally produces no confirmation message, although hooks or channel reassignment can produce output. It does not send an IRC `kill` request.

## Example

```text
/window killswap
```

Replace the current visible window with the first hidden window and destroy the original, subject to the checks and failure behavior above. Use `window swap` alone when you want to retain the original window.

## History

This operation first appeared before EPIC3.004.

## Source references

- [source/window.c](../../source/window.c): `windowcmd_killswap` performs the protection check and unconditional post-swap deletion attempt; `swap_window`, `unlink_window`, and `delete_window_contents` implement replacement, deletion restrictions, and cleanup; `window_check_servers` handles autoclose.
- [source/names.c](../../source/names.c): `reassign_window_channels` finds replacement channel windows.
