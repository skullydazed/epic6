# Ignore script

## Synopsis

```text
/load -pf ignore
/ignore [targets] [operations ...]
```

This is an **unfinished script replacement** for the retired built-in ignore command. Its intended purpose is to suppress unwanted client output by matching a sender or target and an output level. The current implementation does not provide dependable ignore-list management or suppression; do not treat the command forms below as a working privacy or filtering guarantee.

The file has no standard-loader-to-PF wrapper. Load it explicitly with `-pf`, since it contains multiline blocks and explicit statement separators. The default startup scripts do not load it.

## Intended rules and actual state

The script describes targets as nickname/userhost masks, channels, or numeric rule references, with level operations, reasons, and timeouts. It stores rule data under `_ignores` and intends `_ignores_lookup` to map patterns to references. However, its construction and lookup do not currently maintain a consistent rule table.

| Form | Current implementation |
| --- | --- |
| `/ignore` | Calls `ignore.show_all`, which this file never defines; no list display is supplied. |
| `/ignore target` | Attempts to find/create a rule, then calls an empty display helper. |
| `/ignore target levels...` | Attempts to append operation tokens to rule-level data; canonicalization is an empty helper. |
| `reason value` | Stores the next word as reason text when reached by operation parsing. No listing or suppression behavior uses the reason. |
| `timeout seconds` | Stores current epoch time plus the following value. No timer or expiry check enforces it. |

The `ignore` alias takes a target argument and the remaining operation text. It removes commas rather than replacing them with separators, so comma-separated names or levels can be concatenated. The comments' promised comma-list interface should not be relied on.

For new nonnumeric targets, the helper creates a record with a `target` field and initial `ALL` levels, but does not populate the reverse lookup table or return the newly allocated reference. Later operations therefore do not reliably update that record. Numeric references are accepted only when the corresponding stored target exists. There is no implemented mask completion to `nick!user@host` despite the design comments.

The canonicalization, garbage-collection, and display helpers are empty. Thus the former semantics for `+level`, `-level`, `none`, highlighting, exceptions such as `^notices`, exact-pattern removal, and automatic timeout deletion are not restored. See [levels](../special/levels.md) for client output levels, but do not assume this prototype validates or applies every level operation.

## Suppression hook

Loading installs a serial `-333` [context](../on/context.md) hook with quiet noise. It loops through `_ignores`, attempts pattern comparisons against sender and target, checks `levelctl(in ...)`, and calls [shh](../commands/shh.md) on a match. The level check is local client logic, not a request that the server stop sending messages.

The hook reads a field named `pattern`, whereas normal creation stores `target`. It therefore does not use the created target mask as intended. It also has no best-match scoring or longest-pattern selection, no timeout check, and no per-server filtering in the loop despite receiving the server as a hook parameter. The hook's early return after `shh` does not establish the old layered-ignore policy.

`shh` suppresses output for the current client context; it does not undo state updates, remove prior history, block network traffic, or stop another script from handling an event. Hooks and variable names installed by this prototype can also conflict with other script definitions.

## Practical use and dependencies

The former example `/ignore foobar all` expresses the intended “ignore this sender” action, but the current implementation cannot be documented as successfully providing that action. Likewise, `all -publics`, `^notices`, and `none` examples must not be used as evidence of working exception or removal rules.

The script uses EPIC aliases, locals, structures, loops, word helpers, `encode`, `time`, `levelctl`, and context hooks. Some compatibility helpers are supplied by [builtins](builtins.md), but loading those helpers does not fill in the unfinished ignore routines. There is no external program or optional embedded-language requirement. Command output and failure behavior are incomplete rather than a defined success/error interface.

## Source references

- `script/ignore`: alias, rule fields, incomplete helpers, and context hook.
- `source/alias.c`: argument-list binding, including remaining text in the final named argument.
- `source/functions.c`: `strip` removes matching characters rather than splitting comma lists.
- `source/window.c`: context-hook arguments and `shh` suppression flag.
- `source/screen.c`: checks the output-suppression flag before display.
- `KNOWNBUGS`: retirement of the former built-in ignore subsystem.
