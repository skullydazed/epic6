# $nametoip()

## Synopsis

```text
$nametoip(<hostname>)
$iptoname(<ip address>)
```

## Technical

`nametoip()` resolves a hostname to one numeric IPv4 address. [$iptoname()](iptoname.md) performs reverse lookup for a numeric address. Although the reverse function accepts IPv6, `nametoip()` explicitly requests IPv4; it does not return an IPv6 address or a list of all addresses.

The entire input is the hostname: it is not split into argument words or dequoted. Supply it without surrounding quotes or extra arguments. A numeric IPv4 address is accepted too; it is not rejected merely because it is already an address. `localhost`, compared case-insensitively, is explicitly mapped to `127.0.0.1` before resolution.

Resolution uses the client's c-ares resolver with address-configuration filtering and sorting disabled. If several addresses are returned, EPIC selects the first IPv4 result in resolver order. That order depends on resolver results and configuration, not a selection of the “best” address by this function.

The function waits for the callback while processing EPIC's event loop. Other events and scripts can run before it returns. There is no callback argument, timeout option, or family selector in this function. It does not send an IRC request or connect to the resolved address.

## Practical

Use this when the input is a hostname and you want an IPv4 address. [$convert()](convert.md) provides conversion for input that may instead be a numeric address. A hostname without a usable IPv4 result can fail even if it has an IPv6 address.

## Returns

One IPv4 address in dotted-decimal form. Resolution or numeric-formatting failure prints a system-error diagnostic and returns the empty string. The wrapper ignores the helper's status and returns its output buffer, initially empty; it supplies no separate error code.

The output buffer allows at most 255 bytes, more than enough for a numeric IPv4 address. Resolver configuration and records determine which address is returned. The built-in is always registered and uses the client's c-ares resolver.

## Examples

| Expression | Expected or possible result |
| --- | --- |
| `$nametoip(localhost)` | Normally `127.0.0.1` |
| `$nametoip(127.0.0.1)` | Normally `127.0.0.1`; numeric input is accepted |
| `$nametoip(example.com)` | One IPv4 address if resolution succeeds |
| `$nametoip(::1)` | Empty with a diagnostic; this function requests IPv4 |
| `$iptoname(127.0.0.1)` | A reverse hostname or the numeric fallback; see `$iptoname()` |

Registration, whole-input handling, and output buffer: [source/functions.c](../../source/functions.c), `function_nametoip()`. IPv4 selection, resolver flags, callback waiting, and numeric formatting: [source/network.c](../../source/network.c), `hostname_to_paddr()`, `hostname_to_ssu()`, and `ssu_to_paddr()`.
