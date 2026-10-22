# window lister

## Synopsis

```text
/window [<window>] lister "<references>"
```

## Description

Display a summary of the specified windows, in the supplied order. Each reference can be a window number or name. Quote a space-separated list to pass it as one argument; commas are not list separators.

Output includes the reference number, server nickname, window name, channel, query, server, and levels, with a hidden-window indicator where applicable. A heading is printed when more than one supplied reference resolves to a window. Invalid references produce a missing-window message.

The optional window selector does not restrict the list. This operation does not select or show the listed windows. No argument, or `/window -lister`, produces no listing. See [list](list.md) to list all windows.

## Examples

```text
/window lister "1 3 chat"
```

## Source references

`windowcmd_lister` and `list_a_window` in [source/window.c](../../source/window.c).
