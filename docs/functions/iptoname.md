# iptoname function

## Synopsis

`$iptoname(<ip address>)`

## Technical

Looks up a hostname for a numeric IPv4 or IPv6 address. [$nametoip()](nametoip.md) converts a hostname to an address in the opposite direction. Input is first parsed with the system's numeric-only address parser, then reverse lookup is requested through c-ares.

The entire input is the address: it is not split into argument words or dequoted. Supply the address without surrounding quotes or extra arguments. Hostnames such as `localhost` fail numeric parsing. This function does not make an IRC request or connect to the address.

The function waits for the lookup callback while processing EPIC's event loop. Other events and scripts can run before it returns; it is not an immediate lookup and does not accept a script callback argument.

## Practical

Use this when you know the input is an IP address and want its reverse hostname. [$convert()](convert.md) is available for input that may instead be a hostname. Reverse lookup results depend on resolver configuration and the address owner's records; they are not proof of the address's identity.

## Returns

The hostname copied into a buffer of at most 255 bytes. If the resolver supplies no hostname, the callback instead copies the numeric address into that buffer. The wrapper ignores the lookup status, so a failed reverse lookup can still return that numeric fallback while printing an error diagnostic. Do not treat every nonempty result as a successful hostname lookup.

Invalid numeric input, including omitted input, returns empty with numeric-parser diagnostics. Reverse lookup failures can also print a system-error diagnostic. There is no separate error code, and an overlong hostname is truncated to fit the buffer.

## Examples

| Expression | Possible result |
| --- | --- |
| `$iptoname(127.0.0.1)` | `localhost`, another locally configured name, or `127.0.0.1`. |
| `$iptoname(::1)` | A configured loopback hostname, or `::1`. |
| `$iptoname(localhost)` | Empty string with a diagnostic; input is not numeric. |

## Restrictions

The built-in is registered unconditionally and uses the client's c-ares resolver. There is no guarantee that a reverse name exists or that the call will return immediately. During the nested event loop, state such as the current window or server can change through other events.

## Source

Registration, whole-input handling, fixed output buffer, and ignored lookup status: [source/functions.c](../../source/functions.c). Numeric parsing, reverse lookup, numeric fallback, diagnostics, and event-loop waiting: [source/network.c](../../source/network.c).
