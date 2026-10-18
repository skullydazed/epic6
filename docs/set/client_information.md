# set client_information

## Synopsis

```text
/set client_information [<text>]
/set -client_information
```

Default in this source tree: `What next?`, from the build's `IRCII_COMMENT`. With no text, [set](../commands/set.md) displays the current value. Text can contain spaces; `/set -client_information` unsets it rather than restoring the default.

## Description

The contents of this variable are appended to the client's supplied CTCP [version](../ctcp/version.md) reply. In EPIC6 the setting is a C-registered string, but the responder that reads it is supplied by [script/ctcp](../../script/ctcp), loaded by [script/builtins](../../script/builtins). A setup that does not load that responder will not acquire one merely by changing this setting.

The supplied responder builds this text:

```text
ircII <client-version> - <client-information>
```

The version portion comes from `$J`; this setting changes only the final information portion. The script reads the current value when handling a request, so changing it affects later replies. It does not automatically add operating-system information, alter the compiled version, or change the separate CTCP `userinfo` response.

Unsetting the string leaves the responder's other text in place; it does not disable responding to version requests. Custom CTCP handlers can ignore the setting or format it differently. Request hooks, inactive CTCP registrations, and reply throttling can prevent a response. Encoding conversion and IRC message-space limits can alter or truncate the final transmitted reply.

The `set` command changes local state without sending a request. The configured text is sent to remote requesters when the responder replies, so use information you intend to disclose. It is user-supplied identification, not verified information about the client.

## Example

```text
/set client_information Running my own scripts.
```

With the supplied responder active, subsequent version replies end with `- Running my own scripts.`. Query another client's version with `/ctcp nickname version`; that request reports the other client's chosen information.

## Source references

- [source/vars.c](../../source/vars.c): registers the string and derives its default from `IRCII_COMMENT`; standard string query and unset handling.
- [include/irc.h](../../include/irc.h): defines the current build comment.
- [script/ctcp](../../script/ctcp): version request handler reads `$CLIENT_INFORMATION` and constructs the reply.
- [script/builtins](../../script/builtins): loads the CTCP responder script.
- [source/ctcp.c](../../source/ctcp.c): request dispatch, active handlers, reply throttling, and transmission.
