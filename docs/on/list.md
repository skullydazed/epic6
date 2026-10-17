# on list

## Synopsis

```text
/on [<noise>]list "<pattern>" [(<argument list>)] { <commands> }
```

## Description

Triggered for channel-list rows received from the server in response to [list](../commands/list.md), and for the list-start header when the server sends one. It does not run for every possible reply to a list request: the end-of-list numeric and server errors use numeric handling instead.

Use standard [on](../commands/on.md) matching and noise controls. The pattern matches the channel, user count, and topic together. There are no event-specific options. Which channels, counts, and topics the server reveals depends on its list implementation and visibility rules.

## Parameters

| Parameter | Meaning for a channel row |
| --- | --- |
| `$0` | Channel name or server-provided channel placeholder. |
| `$1` | User-count text supplied by the server. |
| `$2-` | Topic text, possibly empty. |

A list-start numeric `321` generates the synthetic fields `Channel Users Topic`, regardless of its original text. Thus `$1` is not always numeric and `$0` does not always name a channel. A row numeric `322` must contain channel, count, and topic parameters; a missing topic parameter is rejected as malformed, while an explicitly empty topic is accepted.

## Default behavior

If the hook permits default handling, processing continues through the corresponding numeric hook (`321` or `322`), then [on numeric](numeric.md), before built-in display. A suppressing hook, such as a serial-zero `^list` hook, skips that later handling and its default line for this item.

The default header is `Channel Users Topic` with EPIC's message prefix. Channel rows display the channel, user count, and topic. [Set channel_name_width](../set/channel_name_width.md) controls channel padding and truncation; its default `0` uses the full channel name followed by a tab. A server placeholder beginning with `*` is displayed as `Prv`, while this hook receives the original placeholder.

Channel rows use an `other`-level context without a channel target, so they are not routed as ordinary output for the listed channel. Handling the event does not join channels, alter the server's list results, or send another IRC request. Suppressing one row does not cancel the rest of the listing.

## Example

Display only actual channel rows with nonempty topics, replacing their ordinary display and dropping the synthetic header:

```text
on ^list "*" (channel, users, topic) {
    if (channel != [Channel] && strlen($topic)) {
        xecho -b $channel $users $topic;
    };
};
```

The separate end-of-list numeric can still display; it is not covered by this hook.

## Source references

- [source/numbers.c](../../source/numbers.c): numeric `321` creates header arguments; numeric `322` validates row fields, sets routing, and invokes the hook; subsequent numeric hooks and display implement formatting.
- [include/config.h](../../include/config.h): defines the default `channel_name_width` as zero.
- [source/hook.c](../../source/hook.c): event registration and `do_hook` implement matching and suppression.
