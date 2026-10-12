# getuid function

## Synopsis

`$getuid()`

## Technical

Gets the real user ID of the running EPIC process using the Unix `getuid()` system interface. This is a numeric process credential, rather than a login name or the effective user ID used for many permission checks.

No arguments are required; supplied arguments are ignored. The function does not look up another user's ID or change any process credentials. Its result is independent of IRC servers, nicknames, and environment variables such as `USER`.

## Returns

The process's real user ID as an integer string. The wrapper has no separate error return or diagnostic.

## Examples

```irc
@ real_user_id = getuid()
```

This stores EPIC's current real user ID in `real_user_id`. See [$getgid()](getgid.md) for its real group ID and [$getlogin()](getlogin.md) for the login session name, when available.

## Source

Registration and the direct `getuid()` call: [source/functions.c](../../source/functions.c). Integer-string return formatting: [include/functions.h](../../include/functions.h).
