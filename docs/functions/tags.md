# tags

## Synopsis

`$tags()`

## Technical

Returns the raw IRC message-tag token from the message currently being parsed. The leading `@` is excluded, and the token ends at the first ASCII space. For a line beginning `@aaa=bbb;ccc;example.com/ddd=eee`, the result is `aaa=bbb;ccc;example.com/ddd=eee`.

Tag order, name casing, semicolons, equals signs, and backslash escapes are preserved. The function does not split tags, select a key, decode escaped values, validate tag syntax, or distinguish a valueless tag from an explicitly empty value. All arguments are ignored: `$tags(account)` still returns the complete token.

## Practical

Read tags inside a callback for a parsed server message, such as an [`on msg`](../commands/on.md) handler. Copy the result into a local variable before running code that could parse another message. Scripts must parse the returned token and decode values themselves.

This is a local accessor. It does not request tags, negotiate IRC capabilities, or query a server; the server must actually supply a tag-prefixed message. Inbound recoding and configured inbound mangling occur before the tag token is extracted, so it is not necessarily a copy of the original network bytes.

## Returns

The complete raw tag token, or the empty string if the parsed message has no tag prefix. There are no options or build-specific requirements.

## Restrictions

The tag state is global, rather than per server or saved per callback. Parsing another message overwrites it, and nested parsing does not restore the outer message's tags. The parser also leaves the pointer referring to its temporary message buffer when parsing ends; do not rely on `$tags()` from the input line, a later timer, or other code outside the active message callback.

The [`on raw_irc`](../commands/on.md) hook runs before extraction, so `$tags()` there does not represent the incoming line's tags. Read the raw hook arguments instead.

## Examples

```irc
on ^msg "*" {
    local message_tags $tags();
    echo Message tags: $message_tags;
}
```

For an incoming message with `@account=alice;label=abc`, the saved value is `account=alice;label=abc`. A tagless incoming message supplies an empty value.

## Source

Handler: [`source/functions.c`](../../source/functions.c:9457). Tag extraction: [`BreakArgs`](../../source/parse.c:214). Callback ordering and lifetime: [`parse_server`](../../source/parse.c:1437).
