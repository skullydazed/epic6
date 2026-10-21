# List

For the IRC channel directory, see the [list command](../commands/list.md):

```text
/list [server-specific arguments]
```

It asks the server for channel names, user counts, and topics. Replies arrive asynchronously; the server controls supported filters, channel visibility, permissions, and rate limits. An explicit channel name conventionally requests information about that channel, but supported query forms vary by network.

EPIC6's built-in forwards the argument text. It does not implement the former local `-min`, `-max`, `-public`, `-private`, `-topic`, `-wide`, `-name`, or `-users` filtering/sorting interface, and does not replace `*` with the current channel. Do not assume these options will filter locally or reduce network traffic. Large listings can still produce substantial output.

For other meanings:

- [on list](../on/list.md) handles the header and channel entries received from a listing.
- [window list](../window/list.md) displays the client's windows.
- [What is a word?](../special/what_is_a_word.md) explains word lists used by script functions.
- [listarray](../functions/listarray.md) returns names of client-side arrays.

## Source references

- `source/commands.c`: `list` registration and generic protocol sender.
- `source/numbers.c`: listing replies and `list` hook delivery.
- `source/window.c`: window listing.
