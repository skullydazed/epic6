# cpu_saver

## Synopsis

```text
/bind <key> cpu_saver
```

This binding action has been removed from EPIC6. The syntax above is retained to identify obsolete script bindings; it does not install a built-in action.

## Description

In earlier versions, this keybinding immediately switched the client to CPU saver mode. That mode reduced the frequency of periodic clock, notify, and mail checks while the user was away, using `cpu_saver_every` instead of their usual intervals.

EPIC6 no longer implements CPU saver mode or registers the `cpu_saver` binding action. It also does not register the `cpu_saver_after` or `cpu_saver_every` settings. There is no default key assignment or supported argument list for this removed action.

Attempting to bind a valid key sequence to `cpu_saver` in an unmodified EPIC6 session reports `No such function cpu_saver` and leaves the binding unchanged. A script could define an action with this name, but its behavior would belong to that script.

For the remaining built-in clock timer, [clock_interval](../set/clock_interval.md) controls the update interval; CPU saver no longer overrides it. Remove obsolete CPU saver bindings from startup scripts. See [bind](../commands/bind.md) for supported binding operations.

## Example

This obsolete binding produces the diagnostic described above:

```text
/bind ^X^S cpu_saver
```

## Source references

- [source/keys.c](../../source/keys.c): `init_binds` lists built-in actions; `bind_compressed_string` rejects unknown action names before modifying a binding.
- [source/vars.c](../../source/vars.c): setting registration includes the clock settings and excludes the retired CPU saver settings.
- [source/clock.c](../../source/clock.c): the system clock timer uses `clock_interval` directly.
- [UPDATES](../../UPDATES): the March 8, 2025 EPIC6 feature retirement note records the removal of CPU saver.
