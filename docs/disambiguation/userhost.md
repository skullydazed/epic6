# Userhost

Do you mean:

- The [userhost command](../commands/userhost.md), `/userhost [options] [nicknames...]`, which requests username/hostname information and displays results or invokes a callback? Callback requests can use cached results synchronously unless forced through the server path.
- The [userhost function](../functions/userhost.md), `$userhost([nicknames...])`, which returns current sender information or reads local channel caches without sending a request?

With no arguments, the function reads the current message sender's `user@host` context; outside suitable message processing it is normally empty. With nickname arguments, unknown cached addresses are represented as `<UNKNOWN>@<UNKNOWN>`. The command is the appropriate interface for requesting fresh server information, but it does not synchronously return that reply as a function value.

Userhosts are server-supplied text and may contain cloaked hostnames rather than IP addresses. See the selected page for callback timing, flags, cache limitations, and server-dependent results.

## Source references

- `source/who.c`: command request queue, cache-assisted callbacks, and reply handling.
- `source/functions.c`: sender-context and nickname-cache function forms.
- `source/names.c`: channel userhost cache.
