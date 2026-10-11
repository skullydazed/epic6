# $convert()

## Synopsis

`$convert(<ip address|hostname>)`

## Technical

Looks up an Internet address or hostname. It first tries to interpret the entire input as a numeric IPv4 or IPv6 address and obtain its hostname. If that fails, it tries an IPv4 forward lookup of the same input and returns one numeric address.

There is no word-by-word argument parsing: supply just the address or hostname, without surrounding quotes or extra arguments. The forward lookup selects one result, not a list of every address. `localhost` is explicitly treated as `127.0.0.1` on that path.

An IPv4 address without a usable reverse lookup can therefore return the same numeric address through the forward-lookup fallback. Hostname-to-address conversion is limited to IPv4; numeric IPv6 input can be reverse-looked up, but there is no IPv6 forward-lookup fallback.

## Practical

This function combines the work of [`$iptoname()`](iptoname.md) and [`$nametoip()`](nametoip.md). Use it when you do not know whether the input is an address or a hostname.

The function waits for lookup completion before returning, while continuing to process EPIC's event loop. Other events and their scripts can run during that wait. Results depend on the resolver and available host records, not the IRC server.

## Returns

Returns the hostname or numeric IPv4 address found by the lookup. Lookup failures can produce an empty string and a diagnostic. A failed reverse-lookup callback can also leave a numeric address in the result buffer, so a nonempty result alone does not prove that a lookup succeeded.

The result buffer is 256 bytes; a longer hostname copied by the reverse-lookup callback is truncated to fit. No structured error code is returned.

## Examples

`$convert(some.school.edu)` returns an IPv4 address if that hostname resolves. `$convert(192.168.1.1)` returns its hostname if reverse lookup succeeds, or may return the numeric address through the fallback.

```irc
alias dns {
    echo Matched $0 to $convert($0)
}
```

## Source

Registered and implemented in [source/functions.c](../../source/functions.c). Lookup ordering, address handling, c-ares callbacks, and event-loop waits are in [source/network.c](../../source/network.c).
