# renumber script

## Synopsis

```text
/load renumber
/renumber
/set renumber_auto on|off|toggle
```

## Description

The `renumber` alias renumbers all your window refnums so there are no gaps, starting at `1`. For example, windows numbered `1`, `3`, and `5` become `1`, `2`, and `3`, respectively. Their existing numeric order is preserved.

The command obtains all window refnums, sorts them numerically, and applies [window number](../window/number.md) to each window needing a different number. This includes hidden windows and windows on other screens or server connections. It does not restrict operation to the current screen or server, accept a starting number, or support a selected subset. Extra arguments are unused.

Renumbering changes user-visible identifiers, not window contents, names, channels, queries, visibility, screen positions, or internal window identities. Scripts and commands referring to an old numeric refnum may subsequently select a different window. Hidden-window selection commands that use numeric order also see the new numbers.

## Automatic renumbering

The script-provided Boolean setting `renumber_auto` controls whether `renumber` runs after a [window_kill](../on/window_kill.md) event. Its default is `off`, explicitly reapplied when the script loads. The supported name is `renumber_auto`, not `auto_renumber`.

Turning the setting on does not immediately close existing gaps; run `/renumber` to do that. Thereafter, normal window destruction invokes the script's serial-384 wildcard hook after the killed window has been removed. Hiding or renaming a window does not invoke it, and normal client shutdown skips that event. Turning the setting off leaves manual `/renumber` available.

## Requirements and limits

Load [builtins](builtins.md) first in a minimal configuration: it supplies the `winrefs` helper and loads [addset](addset.md). This script calls both helpers without loading either dependency itself. The `renumber_auto` setting is created by `addset`; it is not a built-in setting. No external program, IRC request, or optional platform feature is involved.

Successful renumbering normally produces no confirmation. Use `/window list` to inspect the new numbers. There is no function-return contract, rollback, or special recovery if a window operation fails; ordinary window diagnostics apply. Another script replacing the same serial-384 window-kill hook can displace automatic renumbering. The alias also explicitly deletes a variable named `i` after its loop, so avoid relying on a global variable of that name surviving a call.

## Examples

```text
/load renumber
/renumber
/window list
/set renumber_auto on
```

This closes existing gaps, displays the resulting window list, and enables renumbering after later window deletions.

## History

This script first appeared in EPIC5-0.3.1. The retained source is marked as 2006 work by weirdo.

## Source references

- [script/renumber](../../script/renumber): numeric sorting, renumber loop, setting, and automatic hook.
- [script/builtins](../../script/builtins): `winrefs` returns `windowctl(refnums)` and `addset` is loaded.
- [script/addset](../../script/addset): script-provided setting creation.
- [source/window.c](../../source/window.c): `windowctl(refnums)` enumerates user refnums; `windowcmd_number` updates them; destruction invokes `window_kill` after removing the window, except during shutdown.
