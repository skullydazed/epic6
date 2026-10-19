# set user_information

## Synopsis

```text
/set user_information [<text>]
/set -user_information
```

Default: `EPIC6 -- Did we get lost again?`. With no text, [set](../commands/set.md) displays the current value. Text may contain spaces; unsetting removes the stored string.

## Description

Contains the text returned to a remote client requesting CTCP USERINFO when the supplied CTCP responder script is active. The text can be anything, or absent.

The setting itself is a C-registered string. The responder is script-provided: [script/ctcp](../../script/ctcp), loaded by `builtins`, registers a USERINFO request handler that reads `$USER_INFORMATION` and passes it to the CTCP reply command. Changing the value affects later requests handled by that script, without reloading it. A replaced or disabled handler can behave differently.

Unsetting the text does not disable the handler. It can still send an argumentless USERINFO reply. To stop replies, change the CTCP handler policy rather than relying on an absent string.

## Effects and limitations

Assigning this setting does not immediately transmit anything. A reply depends on an incoming request reaching the handler and on the outgoing CTCP and server-send paths. Message-length limits, outbound mangling, hooks, and server behavior can affect what is delivered.

The string is separate from your nickname, registration realname, and [client_information](client_information.md) used by the supplied VERSION responder. It does not change any of those values. The responder uses the stored string as its data; ordinary command parsing can expand expressions while assigning that string.

## Example

```text
/set user_information Usually available in the evening
```

A later USERINFO request handled by the supplied script replies with that text, subject to normal send processing. `/set -user_information` removes the text while leaving the handler active.

## Source references

- [source/vars.c](../../source/vars.c) and [include/config.h](../../include/config.h): string registration and default text.
- [script/ctcp](../../script/ctcp) and [script/builtins](../../script/builtins): USERINFO request handler and script loading.
- [source/ctcp.c](../../source/ctcp.c): request dispatch and CTCP reply formatting.
- [source/commands.c](../../source/commands.c): `ctcpcmd` sends a reply when called in incoming PRIVMSG context and permits an argumentless reply.
