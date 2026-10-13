# $serverwin()

## Synopsis

`$serverwin([server refnum])`

## Technical

Returns the user-visible refnum of the highest-priority window assigned to the specified server. Without an argument, it uses the current execution server's refnum. Only the first integer argument is used; additional arguments are ignored. Server names and descriptions are not accepted here; use [$serverctl(refnum ...)](serverctl.md) to resolve them first.

The lookup considers all windows, including hidden windows. It compares their stored server assignment and chooses the greatest current-window priority; equal priorities retain the first window encountered. It does not change input focus or any window assignment.

There is no server-entry validity check in this lookup. In particular, explicit `-1` is matched literally against windows assigned to no server; it is not an alias for the execution server. Omitting the argument also searches unassigned windows if the execution server is `-1`. Integer parsing is permissive: nonnumeric input becomes zero rather than reporting an invalid argument.

## Practical

Each time a window is made the client's current input window, its priority records that recent selection. Among a server's windows, the highest-priority one is consequently its most recently selected input window, called the server's current window.

[/window](../commands/window.md) operations and window-navigation key bindings can change which window is current. This function locates the current window for a particular server even when another server's window currently has input focus. A hidden window can retain the highest priority.

## Returns

A positive window refnum, or `-1` when no window has the requested server assignment. An unknown nonnegative server refnum normally has no matching window and therefore returns `-1`. A connection need not currently be open for a window assignment to match. There are no options.

## Examples

`$serverwin()` returns the highest-priority window for the current execution server, or `-1` if none matches.

`$serverwin(2)` returns that window's user-visible refnum for server 2, or `-1` if no window is assigned to server 2.

`$serverwin(-1)` searches windows assigned to no server.

Registration and `function_serverwin()`: [source/functions.c](../../source/functions.c). Window traversal, priorities, and `get_server_current_window()`: [source/window.c](../../source/window.c). Integer parsing: [include/functions.h](../../include/functions.h).
