# window delete

## Synopsis

```text
/window [<window>] delete
```

## Description

Destroy the auxiliary physical screen containing the selected window, such as a screen created with [window create](create.md). With no window selector, operate on the current window's screen. The main, first screen cannot be deleted.

Every EPIC window on the deleted screen becomes invisible rather than being killed. Its channels, targets, and stored output remain available, and it can later be brought onto another screen with a supported window showing or swapping operation. The screen's input line is destroyed, so text being edited on that screen is not preserved as part of its windows.

The built-in operation closes the screen's data and control connections and removes its input focus and screen-local window stack. When deleting the input screen, EPIC returns its input-screen selection to the main screen and selects an available current window. Successful deletion reports `The screen is now dead.` The `wserv` helper exits when its client data connection closes.

This operation does not kill EPIC windows, part their channels, disconnect IRC servers, or send an IRC request. Use [window kill](kill.md) to destroy a window. [window delete_kill](delete_kill.md) combines deletion of an auxiliary screen with killing the selected window; other windows from that screen remain invisible.

## Arguments and errors

There are no operation-specific arguments or options. Additional words are processed as subsequent window operations. `/window -delete` is a no-op.

Selecting a window on the main screen reports `You may not kill the main screen` and leaves it intact. Selecting an invisible window reports `You may not kill the invisible screen.` and makes no screen change. A window selector identifies the screen through that window; it is not a screen number.

## Examples

```text
/window delete
/window 3 delete
```

Delete the current auxiliary screen, or the screen containing window 3. After returning to the main screen, an invisible window can be restored by swapping it into the current window's place:

```text
/window swap 3
```

See [window swap](swap.md) for its selection and swappability requirements.

## Source references

- [source/window.c](../../source/window.c): `windowcmd_delete`, `windowcmd_delete_kill`, and `add_to_invisible_list` implement screen selection and window preservation.
- [source/screen.c](../../source/screen.c): `kill_screen` performs connection teardown, input-line destruction, focus changes, and diagnostics.
- [source/wserv.c](../../source/wserv.c): the helper exits on closure of its data connection.
