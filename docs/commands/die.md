# die command

## Synopsis

```text
/die
```

## Description

`die` requests that the IRC server you are connected to shut down. It is a server-administration command; it does not terminate EPIC itself. Use [`quit`](quit.md) to exit the client normally.

The C built-in is a pass-through command. With no arguments, it sends `DIE`. Any supplied text is forwarded after `DIE` without client-side validation. The conventional protocol form has no parameters, but individual server implementations may require additional confirmation or use different rules.

EPIC uses the current command's server context, falling back to the primary server when no context is set. The command does not check your operator status, ask for confirmation, wait for shutdown, or report success by itself. A successful shutdown can disconnect clients and break the server's network links. Errors and other replies are handled asynchronously through the normal server message handlers and [`on`](on.md) hooks.

## Restrictions

This request normally requires IRC-operator privileges and may be unavailable even to an operator. Availability, authorization, shutdown behavior, and configuration requirements are determined by the server. The historical `OPER_DIE` and `LOCAL_DIE` compile-time switches are not universal requirements for current IRC server software.

The EPIC command is available in every build and has no special client configure-time requirement. It needs a usable connection to send the request. If the selected server connection is closed, the sending layer normally reports that you are not connected; a `disconnect` hook can suppress that message. With no usable server record, nothing is sent.

Under the conventional IRC protocol, this is processed by the connected server rather than forwarded to another server. Supplying another server's name does not make EPIC target that server; all argument interpretation belongs to the receiving server.

## Example

An authorized operator can request shutdown of the current server with:

```text
/die
```

## Source

See [source/commands.c](../../source/commands.c) for the `die` registration and `send_comm()`; the shared handler has no special `die` branch. [source/server.c](../../source/server.c) implements server selection and connection checks. [RFC 2812, section 4.3](https://www.rfc-editor.org/rfc/rfc2812#section-4.3) describes the conventional server-side request; individual servers can differ.
