# getgid function

## Synopsis

`$getgid()`

## Technical

Returns the real group ID of the running EPIC process, using the Unix `getgid()` system interface. This is a numeric process credential, rather than a group name, an effective group ID, or a list of supplementary groups.

No arguments are required; supplied arguments are ignored. It does not look up another user's group or change any process credentials. The result is independent of IRC servers and nicknames.

## Returns

The process's real group ID as an integer string. The wrapper has no separate error return or diagnostic.

## Examples

```irc
@ real_group_id = getgid()
```

This stores the current process's real group ID in `real_group_id`. See [$getuid()](getuid.md) for its real user ID.

## Source

Registration and the direct `getgid()` call: [source/functions.c](../../source/functions.c). Integer-string return formatting: [include/functions.h](../../include/functions.h).
