# set mangle_outbound

## Synopsis

```text
/set mangle_outbound [<rules>]
/set -mangle_outbound
```

Default: unset, with no outbound mangling. With no rules, [set](../commands/set.md) displays the current value. Unsetting the variable disables this pass.

## Description

Specifies mangling (postprocessing) of data just before it is sent to the IRC server. It can remove special formatting you do not want to accidentally send to others, such as colors.

Separate rules with commas or spaces. Rules modify the existing selection; prefix a rule with a hyphen to remove it. Names are case-insensitive. Start with `none` to replace the selection. Unknown names are silently ignored, and the stored value is rewritten as a canonical list of enabled rules.

Supported rules are `normalize` (alias `ansi`), `color`, `bold`, `blink`, `reverse`, `underline`, `alt_char`, `all_off`, `nd_space`, `escape`, `unprintable`, `other`, `all`, and `none`. `-all` clears the selection. See [mangle types](../special/mangle_types.md) and the [mangle_display rule descriptions](mangle_display.md). There is no separately recognized `italic` rule, although `all` includes italic stripping.

`normalize` interprets supported ANSI and color sequences, preserving formatting as IRC control codes. `color` suppresses colors; `unprintable` alone can remove only a sequence's introducing control character, leaving following text. `all` includes normalization and formatting stripping, but excludes `escape`, `other`, and `unprintable`.

## Sending and limitations

The normal server send routine mangles the command and its arguments before converting that part to the server's encoding. When a caller supplies a separate message payload, the payload has already been encoded for its destination and is mangled separately before being appended. This is not an encoding-conversion setting: the normalizer reads UTF-8, so applying it to a payload already converted to another encoding can damage that payload.

Mangling is followed by the normal outgoing line-length limit, CRLF termination, and [on send_to_server](../on/send_to_server.md). That hook sees the resulting encoded line and can suppress sending. The low-level raw server-write routine does not itself apply these rules.

Any nonempty mask invokes the shared normalizer, which also processes IRC attribute controls, expands tabs, and discards Unicode C1 control characters. With an empty selection, this pass is skipped entirely.

These rules apply to protocol text, not only ordinary chat. `normalize` converts CTCP control-A delimiters, and `unprintable` removes them; either can prevent recipients from recognizing CTCP messages, including actions. `all` includes normalization and has the same concern. `other` removes ordinary command text and can make requests invalid. Server acceptance and recipients' interpretation depend on the resulting request.

Changes affect subsequent sends and do not resend or modify earlier messages. Outbound mangling does not guarantee that local echoes or logfile entries match the bytes ultimately sent.

## Examples

Replace the selection with IRC color stripping:

```text
/set mangle_outbound none,color
```

Remove that rule while retaining any others:

```text
/set mangle_outbound -color
```

Disable outbound mangling:

```text
/set -mangle_outbound
```

## History

`set mangle_outbound` first appeared in epic4pre1.401.

## Source references

- [source/vars.c](../../source/vars.c): unset default, registration, and callback.
- [source/server.c](../../source/server.c): `vsend_to_aserver_with_payload` applies mangling, encoding conversion, length limiting, and the send hook; `send_to_aserver_raw` writes directly.
- [source/commands.c](../../source/commands.c): callers supply separately encoded payloads for messages and other requests.
- [source/screen.c](../../source/screen.c): `parse_mangle` and `new_normalize_string` implement rule selection and transformations.
