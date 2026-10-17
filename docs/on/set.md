# on set

## Synopsis

```text
/on [<noise>]set "<pattern>" [(<argument list>)] { <commands> }
```

## Description

Triggered by [set](../commands/set.md) with a variable name, before EPIC applies the requested value or displays that variable's current value. It allows a script to intercept an attempt, substitute its own setting operation, or customize unknown-variable diagnostics. Bare `/set`, which lists settings, does not generate this event.

Use standard [on](../commands/on.md) matching and noise controls. The pattern matches name and value text together. There are no event-specific options. Direct internal setting changes need not pass through this command hook.

## Parameters

| Parameter | Meaning |
| --- | --- |
| `$0` | Uppercased name supplied to `set`, a resolved full name on a second offer, or `set-error` for a diagnostic offer. |
| `$1-` | Requested value text, empty for an ordinary named query, `<unset>` for a leading-hyphen unset request, or diagnostic text. |

For `/set -name`, the leading hyphen is removed from the offered name and the value becomes the literal marker `<unset>`. This marker describes an unset request, not a successful stored value. Value validation and setting-specific callbacks occur later, so the event can see invalid values too.

A uniquely resolved abbreviation can generate two events: first for the supplied abbreviation, then for the complete setting name if the first event allows continuation. An exact name generates only the first normal offer. The hook runs before the requested mutation or query display.

## Suppression and setting changes

A suppressing hook, such as a serial-zero `^set` hook, stops the intercepted operation before it reaches normal assignment, validation, or value display. A handler can issue its own `set` command to supply a replacement. EPIC marks a resolved variable pending around these hook calls, so nested `set` operations on that same variable bypass its normal set-hook offers. This is a per-variable guard, not general nonrecursive behavior: other variables and unknown names can re-enter the hook.

If normal offers permit continuation, a recognized variable is queried or changed according to the command. The hook does not itself perform assignment, send an IRC request, or guarantee that the supplied value will be accepted.

## Diagnostic offers

An unknown name is offered normally first. If not suppressed, EPIC invokes [on unknown_set](unknown_set.md). If that also permits continuation, it generates `set-error No such variable "NAME"` through this hook before printing the error.

An ambiguous name similarly receives its initial offer and then `set-error NAME is ambiguous`. Allowing that diagnostic prints the ambiguity and matching settings. Suppressing it prevents those diagnostics; there is no resolved assignment to perform. Invalid values for recognized settings do not automatically become `set-error` events through this path.

## Examples

Replace one recognized setting request with a fixed value:

```text
on ^set "clock *" {
    set clock on;
};
```

This intercepts queries and unset attempts for the matched name too. The nested assignment bypasses this variable's pending hook offers.

Customize set-error diagnostics:

```text
on ^set "set-error *" {
    xecho -b $1-. Please try again;
};
```

## Source references

- [source/vars.c](../../source/vars.c): `setcmd` parses names and unset requests, resolves abbreviations, makes normal and diagnostic hook offers, manages pending guards, and then queries or calls `set_variable`.
- [source/hook.c](../../source/hook.c): event registration and `do_hook` implement matching and suppression; `set` itself has no global nonrecursion flag.
