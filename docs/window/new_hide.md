# window new_hide

## Synopsis

```text
/window [<window>] new_hide
```

## Description

Create a new hidden window directly. Because it is never made visible, creation does not split, resize, or rearrange the screen's existing windows as [window new](new.md) followed by [window hide](hide.md) would.

The new window is added to the front of the global invisible-window list. The built-in operation does not make it the global current window or a screen's input window. Subsequent operations in the same `/window` command continue targeting the previously selected window, **not the new one**. An explicit window selector does not determine the new window's server: the constructor uses the global current window's server.

See [window new](new.md) for initialization details. The new window receives the lowest unused positive refnum, starts unnamed with no channels or queries, and normally has [window level](level.md) none. It uses the same built-in settings and global defaults as other new windows. EPIC invokes `window_create` with the new user-visible refnum; scripts can configure it or change selection during that hook.

Use [window list](list.md) to find its refnum and [window show](show.md) to make it visible later. Creation alone does not join a channel or send an IRC request.

## Arguments and limits

There are no arguments or options specific to this operation. Following words are processed as subsequent window operations on the original target. `/window -new_hide` also creates a hidden window; the handler ignores the minus form's null argument rather than treating it as a no-op.

Outside fullscreen mode, the constructor silently refuses additional windows. If refnums are exhausted, it reports `window new: All refnums in use, sorry`. Unlike `window new`, this handler ignores the constructor's failure result and still allows subsequent operations on the original target. Successful creation normally produces no confirmation message, although hooks can produce output.

## Examples

```text
/window new_hide
/window list
```

Create a hidden window, then list windows to discover its refnum. If it is window 4:

```text
/window 4 name background
/window show 4
```

Name that window and make it visible. `/window new_hide name background` would instead name the window that was selected before creation.

## History

This operation first appeared during EPIC4.

## Source references

- [source/window.c](../../source/window.c): `windowcmd_new_hide` calls `new_window(-1)` and retains the original target; `new_window` initializes settings, allocates refnums, and invokes `window_create`; `add_to_invisible_list` places the new window at the front of the hidden list.
