# ttyname

## Synopsis

`$ttyname()`

## Technical

Returns the terminal device name associated with operating-system file descriptor `0`, EPIC's standard input, using `ttyname_r`. All arguments are ignored.

The descriptor is fixed: this function does not select the current window's screen, accept an alternate descriptor, inspect standard error, or use EPIC file handles returned by [`open`](open.md). Creating another screen or selecting a hidden window does not change which descriptor it queries.

## Practical

Use this function to identify the device attached to the process's standard input. The result describes that descriptor, not necessarily the terminal of the active EPIC screen.

## Returns

The terminal device pathname, or empty if `ttyname_r` fails, including when standard input is not a terminal or the name does not fit the pathname buffer. Failure is not reported separately.

Background operation has no special branch here: whether a terminal name is returned depends on standard input still referring to a terminal. There are no options, settings, or server requirements.

## Examples

`$ttyname()` might return `/dev/ttyp2`; device naming depends on the operating system.

## History

First appeared in EPIC4pre2.004-19990528.

## Source

Handler: [`source/functions.c`](../../source/functions.c:7459). Separate screen input descriptors: [`source/screen.c`](../../source/screen.c:3652).
