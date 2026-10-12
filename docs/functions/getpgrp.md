# getpgrp function

## Synopsis

`$getpgrp()`

## Technical

Gets the current process group ID of the running EPIC client using the Unix `getpgrp()` system interface. Process groups are used for job control and group signal delivery. This ID is distinct from the process's user or group credentials and need not equal its process ID.

No arguments are required; supplied arguments are ignored. The function queries EPIC's own process group, does not change it, and does not look up another process. It also does not query the terminal's foreground process group; EPIC can belong to a different group when running in the background.

The built-in is registered unconditionally and uses the no-argument Unix interface directly; there is no optional configure feature or alternate implementation for this function.

## Returns

The client's current process group ID as an integer string. The wrapper has no separate error return or diagnostic.

## Examples

```irc
@ process_group = getpgrp()
```

This stores EPIC's current process group ID in `process_group`. For its real user and group credentials, see [$getuid()](getuid.md) and [$getgid()](getgid.md).

## Source

Registration and the direct `getpgrp()` call: [source/functions.c](../../source/functions.c). Integer-string return formatting: [include/functions.h](../../include/functions.h). EPIC's separate foreground process group comparison: [source/term.c](../../source/term.c).
