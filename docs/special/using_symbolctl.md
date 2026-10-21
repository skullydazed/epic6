# Things you can do with symbolctl

[symbolctl](../functions/symbolctl.md) inspects and changes EPIC's symbol table: aliases, global assignments, built-in commands, functions, expandos, and settings. This guide covers common uses. These are local client operations, not IRC server requests.

## Create your own settings

The supplied [addset](../scripts/addset.md) script provides `addset` and `delset` commands. For most scripts it is simpler than managing setting symbols directly:

```irc
load addset
addset my_feature bool {
    echo Feature setting is now $*;
}
set my_feature on
```

At the lower level, creating a setting has three steps:

```irc
@ symbolctl(create my_feature)
@ symbolctl(set my_feature 1 builtin_variable type bool)
@ symbolctl(set my_feature 1 builtin_variable script { echo Feature setting is now $*; })
```

These lines belong in an EPIC script/evaluation context. The braces preserve the callback text for later execution. The first step ensures the symbol exists; `create` returns `1` for a new symbol and `0` when one already exists. It does not clear an existing symbol. Level `1` selects the current symbol definition; larger positive levels walk saved definitions.

The type step returns `1` on success. Supported types are:

| Type | Values accepted through normal `set` handling |
| --- | --- |
| `bool` | `on`, `off`, or `toggle`. |
| `char` | A character occupying exactly one terminal column; only the first decoded character is used. |
| `int` | A nonnegative integer accepted by the setting parser, stored in its integer field. |
| `str` | String text; unsetting can clear the value. |

New setting storage starts as boolean zero; choosing a numeric type initializes its current value to zero, and choosing `str` initializes it to unset. Reapplying the type operation resets the current value rather than preserving it. Use a unique name: the interface can find existing symbols and is not a safe create-only transaction. Its protection for an existing setting specifically rejects manipulation when that setting has a C callback, not every possible built-in setting.

The optional `script` step stores code and returns `1`. Setting the script alone does not run it. When normal setting handling accepts an update, the value is already stored before its callback executes, and the formatted new value is supplied as `$*`. A callback can change the same setting; the pending guard prevents recursively invoking that setting's callback during its own execution.

For example, this callback reverses an attempt to turn the feature off:

```irc
addset cantturnmeoff bool {
    if (* == [OFF]) {
        echo This feature stays enabled.;
        set cantturnmeoff on;
    };
}
set cantturnmeoff on
```

Invalid type names or unsupported operations normally return an empty string. An unsuccessful type operation can still have allocated the initial setting structure, so inspect the resulting symbol rather than assuming failure rolled everything back. Value-validation errors can also produce client diagnostics. See the function reference for individual return conventions.

## Command completion

```text
$symbolctl(pmatch builtin_command li*)
```

This returns a space-separated list of matching registered built-in command names, such as `LICENSE LINKS LIST` when those are registered. Use the singular type `builtin_command`; `builtin_commands` is not accepted. Other supported selectors include `alias`, `assign`, `builtin_function`, `builtin_expando`, `builtin_variable`, and `*` for all symbol types. Results depend on the current symbol table, including script changes. No matches produce an empty string.

This is a building block for completion code; calling it does not install an input completion binding.

## Check whether a built-in exists

```text
$symbolctl(get <symbol> 1 builtin_command)
$symbolctl(get <symbol> 1 builtin_function)
$symbolctl(get <symbol> 1 builtin_expando)
```

If the symbol exists, these return zero when that particular built-in slot is absent, or a nonzero implementation value when present. A missing symbol or nonexistent saved level returns an empty string instead. Treat the nonzero value only as an existence indicator: it is not a stable identifier or a script-callable address.

For example, in an expression-enabled command:

```irc
if (symbolctl(get echo 1 builtin_command)) {
    echo The echo built-in is registered.;
}
```

The test concerns the built-in slot, not whether a same-name alias shadows command dispatch. Names and operation keywords are case-insensitive. Inspecting local variables requires other scope-aware facilities; these operations use the global symbol table.

## Source references

- `source/alias.c`: `symbolctl` creation, matching, level traversal, built-in tests, and setting manipulation.
- `source/vars.c`: setting validation, value formatting, and callback pending guard.
- `script/addset`: script-provided convenience commands.
