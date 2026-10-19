# set status_does_expandos

## Synopsis

```text
/set status_does_expandos [on|off|toggle]
```

Default: `off`. With no argument, [set](../commands/set.md) displays the current value.

## Description

Determines whether dollar expandos, including variables and function calls, in the assembled status bar are evaluated. Normal percent fields in [status_format](status_format.md) work independently of this setting.

When on, EPIC first inserts the normal status fields, then expands the assembled line using the window being drawn as the current window and that window's server as the server context. The expansion receives an empty argument string, not the arguments of a message or command that happened to trigger the update. EPIC then applies display normalization and lays out the status line.

When off, that extra expansion pass is skipped. This does not undo dollar expressions already expanded while assigning a status setting. Preserve literal dollar signs when storing expressions intended to run at status-update time.

## Scope and limitations

The pass operates on the assembled line, so dollar expressions in literal format text or directly inserted status text can participate. Values inserted through the shared formatted-field helper are escaped for dollar signs and backslashes before this pass; a nickname or channel name inserted through that helper is not treated as arbitrary new script code. Do not assume every field uses identical insertion behavior.

Functions are evaluated whenever the status line is regenerated, not on an independent refresh schedule. A value can remain unchanged on screen until another event requests an update. This setting has no immediate status-rebuild callback of its own; its effect is observed on later regeneration.

Expansion adds work for each regenerated line. Keep functions inexpensive and avoid output or state-changing side effects. Normal output is temporarily suppressed during this pass, but that is not a general prohibition on function side effects. This setting does not execute the resulting text as a command block or send an IRC request itself.

## Example

If your format includes the ordinary `%U` user-status field, store a nickname expando for later evaluation:

```text
/set status_user $$N
/set status_does_expandos on
```

The stored `$N` is evaluated when that user field appears in a regenerated status line, in the displayed window's context. Normal user-field visibility rules still apply. Restore the default extra-pass behavior with `/set status_does_expandos off`.

## Source references

- [source/vars.c](../../source/vars.c): boolean registration, off default, and absence of an update callback.
- [source/status.c](../../source/status.c): `make_status` performs percent-field assembly, contextual `expand_alias`, and normalization; `PRESS` escapes inserted arguments when expansion is enabled.
