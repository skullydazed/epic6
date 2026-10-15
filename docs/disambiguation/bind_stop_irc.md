# stop_irc (removed binding)

## Former synopsis

```text
/bind <key> stop_irc
```

## Description

The former `stop_irc` keybinding suspended the client with `SIGTSTP`, returning control to the shell. EPIC6 does not implement or register this binding action.

Attempting to bind a valid key sequence to `stop_irc` in an unmodified EPIC6 session reports `No such function stop_irc` and leaves the binding unchanged. There is no default key assignment or supported argument list for this removed action. In particular, EPIC6 does not assign Control-Z to it. A script could define an action with this name, but its behavior would belong to that script.

Shell job control is separate from EPIC's keybinding system. If the process is stopped through an external mechanism, resuming it is a shell or operating-system operation; this removed binding provides no suspension or resumption behavior in EPIC6.

## Example

This obsolete binding produces the diagnostic described above:

```text
/bind ^Z stop_irc
```

See [bind](../commands/bind.md) for supported binding operations.

## Source references

- [source/keys.c](../../source/keys.c): `init_binds` lists available built-in actions and `init_keys` defines default key assignments; neither includes `stop_irc`. `bind_compressed_string` rejects unknown action names before changing the binding.
