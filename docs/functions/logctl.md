# $logctl()

## Synopsis

```text
$logctl(new)
$logctl(last_created)
$logctl(refnums active|inactive|all)
$logctl(refnum <log-desc>)
$logctl(add <log-desc> [<targets>])
$logctl(delete <log-desc> [<targets>])
$logctl(get <log-desc> <item>)
$logctl(set <log-desc> <item> [<value>])
$logctl(match [<pattern>])
$logctl(pmatch [<pattern>])
$logctl(current)
```

## Technical

Inspects and configures the independent logfiles managed by the [log command](../commands/log.md). A log descriptor is a decimal reference number or a case-insensitive logical name. Numeric descriptors always select reference numbers. These are distinct from the global session log and window logfiles.

| Operation | Return and effect |
| --- | --- |
| `new` | Create an inactive logfile and return its positive reference number. Defaults: numeric logical name, no filename or targets, target type, current server context, all levels, no per-log rewrite or mangle rule. |
| `last_created` | Return the last allocated reference number; it can refer to a logfile since removed. |
| `refnums active\|inactive\|all` | Space-separated references in creation/list order. The selector is required. |
| `refnum <desc>` | Resolve a descriptor to a reference, or return empty when unknown. |
| `add` / `delete` | Add or remove targets from an existing logfile; return `1` after invoking the helper, even if it reports a failure. `delete` removes targets, not the logfile itself. |
| `get` | Return the selected property. |
| `set` | Invoke the property setter and normally return `1`; this is not confirmation that the change succeeded. |
| `match` / `pmatch` | Unimplemented; always return empty. |
| `current` | Reference of the logfile currently being written: `-1` outside a write, `0` for global/window logging, positive for an independent logfile. Useful inside [log_rewrite](../settings/log_rewrite.md). |

Missing required operation/descriptor/property arguments, unknown descriptors or properties, and unsupported operations return empty. Operation/property matching is case-insensitive but uses fixed comparison lengths, not uniform abbreviation rules; use complete names.

## Properties

| Item | Get | Set |
| --- | --- | --- |
| `refnum` | Reference number. | Unsupported. |
| `name` | Logical name or empty. | Set a unique one-word name; `-` clears it. Duplicate names produce a diagnostic without changing it. |
| `filename` | Destination pathname or empty. | Set a one-word filename. Changing an active log closes it and leaves it off. Missing value displays the current filename. |
| `server` | Associated server reference; `-2` means all. | Set a numeric server or `all`; applies to target-mode filtering. |
| `targets` | Comma-separated configured targets/references. | Clear every target first, then add the supplied comma-list. Missing value therefore clears targets. |
| `level` | Rendered level mask. | Set one quoted-word mask, allowing comma-separated levels or quoted space-separated levels. Unsupported levels warn. Missing value clears the mask. |
| `rewrite` | Per-log rewrite template or empty. | Set one quoted template; missing value clears it so global rewriting can apply. |
| `mangle` | Mangle description or empty. | Set one ordinary-word rule; missing value displays the current rule. A zero rule falls back to global log mangling. |
| `status` | `0` off, `1` on. | Required integer: zero turns off, nonzero attempts to open for append and turn on. Requires a filename and successful open. |
| `type` | `TARGET`, `WINDOW`, or `SERVER`. | Choose a type; targets are cleared even when the type is unchanged or invalid. Supply a value. |
| `activity` | Last activity time in Unix seconds. | Reset to now; supplied value is ignored. Creation, on/off operations, and matching output can also update it. |

The positional operation/descriptor/property words honor function-word parsing. Setters reuse command helpers: filename/name/mangle/add/delete consume one ordinary word; server/type/level/rewrite consume one quoted word. Unconsumed extra text is generally ignored. `status` uses base-detecting integer conversion; text `on` converts to zero, so use `1` and `0`.

## Targets and restrictions

Targets are one comma-separated argument. Their meaning depends on type: exact channel/nickname targets, window descriptions, or server references. Server mode has 32 slots and `add all` means all servers; duplicate server additions can occupy multiple slots. The associated `server` property alone does not establish a server-mode target.

Target-name deletion works. Window deletion does not remove its stored target list, and server deletion converts the following comma-list remainder instead of the current element, so it can remove the wrong reference. To replace those lists, use `set targets` or reset the type and add the desired targets.

Setters can print diagnostics and perform file I/O. After setting status, query `get ... status` rather than relying on the setter's `1`. Turning on appends, can create the destination, and writes a start timestamp; turning off closes it with an end timestamp. See the [log command](../commands/log.md) for filtering, mangling, rewrite context, permissions, and path-normalization details. To unregister a logfile, use `/log <desc> kill`.

## Examples

```text
@logref = logctl(new)
@ignored = logctl(set $logref name channel_log)
@ignored = logctl(set $logref targets #example)
echo $logctl(get $logref targets)
```

This creates and configures an inactive logfile; it does not open a file. `$logctl(refnums all)` lists existing logfile references.

## History

This function first appeared in EPIC4-1.1.8. `activity` and `refnums` first appeared in EPIC4-1.1.11.

## Source

Registered and forwarded by `function_logctl` in [source/functions.c](../../source/functions.c). Operations, setters, targets, and matching are in [source/logfiles.c](../../source/logfiles.c); file opening, rewriting, and `current_log_refnum` are in [source/log.c](../../source/log.c).
