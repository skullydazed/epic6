# getlogin function

## Synopsis

`$getlogin()`

## Technical

Returns the login name associated with EPIC's process, using the Unix `getlogin_r()` interface. This is the operating system's login-session name, rather than an IRC nickname or an arbitrary user-name lookup.

No arguments are required; supplied arguments are ignored. The function uses a 256-byte buffer. If `getlogin_r()` fails, including because no login name is available or the buffer is too small, it returns the empty string without an error message.

There is no fallback to the environment's `USER` or `LOGNAME`, or to the password database using the process's user ID. A login-session name need not describe the process's current real or effective user credentials. Use [$getuid()](getuid.md) for the real numeric user ID, or [$getenv()](getenv.md) for an explicit environment lookup.

## Returns

The login name as a string, or the empty string on lookup failure. Running without suitable login-session information, such as in some detached or background environments, can leave this unavailable.

## Examples

```irc
@ login_name = getlogin()
```

This stores the returned login name, or the empty string if the system cannot provide one. It does not print or change the identity.

## Source

Registration, fixed-buffer `getlogin_r()` lookup, and failure handling: [source/functions.c](../../source/functions.c). Return-string copying: [include/functions.h](../../include/functions.h).
