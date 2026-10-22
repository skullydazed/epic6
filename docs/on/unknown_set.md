# on unknown_set

## Synopsis

```text
/on [<noise>]unknown_set "<pattern>" [(<argument list>)] { <commands> }
```

## Description

Triggered when [set](../commands/set.md) finds no matching variable name and the initial [on set](set.md) handler allows processing to continue.

Use standard [on](../commands/on.md) matching, serial numbers, and noise controls. The pattern matches the complete event data.

## Parameters

| Parameter | Meaning |
| --- | --- |
| `$0` | Requested variable name, converted to uppercase and with an unset-prefix hyphen removed. |
| `$1-` | Supplied arguments; `<unset>` for an unset request, or empty when no value was supplied. |

## Behavior

Ambiguous names do not trigger this event. If default handling is allowed, EPIC offers `set-error No such variable "<name>"` to `on set` and displays the error if that hook also allows it. A suppressing serial-zero `unknown_set` handler prevents both the error hook and its display, allowing scripts to implement custom settings.

The hook has nonrecursion protection. It does not create or store a setting automatically.

## Example

```text
on ^unknown_set "MY_SETTING *" {
    echo Custom setting MY_SETTING received: $1-;
};
```

## Source references

- [source/vars.c](../../source/vars.c): `setcmd` normalizes names, performs matching, and dispatches unknown-name errors.
- [source/hook.c](../../source/hook.c): event registration and standard hook dispatch.
