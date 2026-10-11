# $ctcpctl()

## Synopsis

```text
$ctcpctl(all)
$ctcpctl(active)
$ctcpctl(inactive)
$ctcpctl(get <name> <field>)
$ctcpctl(set <name> <field> [<value>])
```

## Technical

Inspects and changes EPIC's registered CTCP handlers. This changes local processing of incoming CTCP requests and replies; calling the function does not itself send a CTCP message. [`/ctcp`](../commands/ctcp.md) sends messages.

Operation names are case-insensitive and accept prefixes, but use the full names to avoid ambiguity: `a` selects `all`, not `active`. CTCP names are uppercased for lookup and creation. Fields are case-insensitive and require their full spelling. The operation, name, and field use normal function word parsing; a setter's value is the entire remaining text.

`set` creates an entry if the name is not registered, before checking the field or value. A new entry is active, has its name as the description, has no callbacks, and has `special`, `raw`, and `replace_args` off. There is no delete operation. Setting an invalid field or value can still leave that new entry registered.

## Options

| Operation | Result |
| --- | --- |
| `all` | Names of every registered entry. |
| `active` | Names whose active flag is on. |
| `inactive` | Names whose active flag is off. |
| `get <name> <field>` | The selected property. |
| `set <name> <field> <value>` | `1` for a recognized, accepted assignment. |

Lists follow registration order, include a trailing space when nonempty, and can be truncated by the internal output buffer. List operations ignore later arguments.

| Field | `get` | `set` |
| --- | --- | --- |
| `request` | Stored script body, or empty if none. | Store the script body for incoming requests. |
| `response` | Stored script body, or empty if none. | Store the script body for incoming replies. |
| `description` | Description text. | Store literal description text, not an alias name. |
| `special` | `0` or `1`. | Accept exactly `0` or `1`. |
| `raw` | `0` or `1`. | Accept exactly `0` or `1`. |
| `active` | `0` or `1`. | Accept exactly `0` or `1`. |
| `replace_args` | Unsupported: empty string. | Accept exactly `0` or `1`. |

Boolean values must be exactly `0` or `1`, without extra words or trailing spaces. Text fields may be set to an empty string. An empty callback body remains a user callback and still overrides the corresponding C handler; it does not restore that handler. `get request` and `get response` return script bodies, not C implementations.

## Handler behavior

Script callbacks run as functions in a fresh local scope in the incoming CTCP context. Their arguments are `$0` sender, `$1` receiver (your nickname or a channel), `$2` CTCP name, and `$3-` CTCP arguments. [`/return`](../commands/return.md) supplies the callback's result.

With `replace_args` on, the callback result replaces the CTCP arguments used for subsequent notification. With it off, a returned string replaces the accumulated message text before the CTCP; text after the CTCP is then appended. This can discard ordinary text preceding the CTCP, rather than merely replacing the CTCP itself.

Request callbacks return a string even if no explicit value is returned: an empty result still participates in replacement. Empty results from script response callbacks are instead treated as no replacement. Returning text does not automatically suppress the usual CTCP notification. The `special` flag skips that notification for an entry with an applicable callback, including when its callback returns nothing useful.

`raw` supplies the original CTCP argument bytes to the callback instead of the recoded UTF-8 arguments. It does not disable earlier request hooks' recoding. `active` controls request callback execution only: turning it off does not disable reply callbacks, earlier request hooks, or ordinary notifications. A registered entry without a callback for the incoming direction falls through to ordinary handling regardless of these flags.

## Practical

The C initialization registers `ACTION`. Other handlers can be installed by scripts; for example, [script/ctcp](../../script/ctcp) provides handlers such as `VERSION`, `PING`, and `CLIENTINFO`, and uses descriptions for its `CLIENTINFO` replies. Those replies are script behavior, not an automatic effect of assigning a description.

## Returns

Returns the selected list, text property, boolean value, or `1` for an accepted setter. Missing required arguments, unsupported operations or fields, and rejected boolean values return an empty string. Getter arguments after the field are ignored.

## Restrictions

Only query names known to be registered, such as names from `all`. The current implementation does not check a failed name lookup before accessing a supported getter field; querying an unknown name can crash EPIC instead of returning an empty string.

Incoming CTCP dispatch compares names exactly against registered uppercase names; lowercase incoming CTCP names do not select the registered callback. Earlier hooks and normal CTCP message-processing limits can prevent a callback from running. `raw` callback data is not necessarily valid display text.

## Examples

```irc
echo Registered CTCPs: $ctcpctl(all)
echo ACTION is active: $ctcpctl(get ACTION active)
@ ctcpctl(set DEMO description Demonstration CTCP)
@ ctcpctl(set DEMO request {return Demonstration text})
@ ctcpctl(set DEMO special 1)
```

The last three calls register a handler that replaces the message prefix with `Demonstration text` and skips its ordinary CTCP notification. Each setter returns `1`; registration does not send a request.

## Source

Registered in [source/functions.c](../../source/functions.c). Control operations, registration, and incoming dispatch are in [source/ctcp.c](../../source/ctcp.c). Callback scope and function returns are implemented in [source/commands.c](../../source/commands.c).
