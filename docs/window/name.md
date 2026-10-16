# window name

## Synopsis

```text
/window [<window>] name [<name>]
/window [<window>] name -
/window [<window>] -name
```

## Description

Assign the selected window a name. With no window selector, use the current window. The name can be used in place of its reference number where a command accepts window names. Use [window number](number.md) to change the window's reference number instead; naming does not change it.

Names are compared case-insensitively and must be unique across all windows, including hidden windows and windows on other servers. Assigning the existing name again is allowed, including changing only its capitalization. New windows start unnamed.

A double-quoted argument can contain spaces, as in `/window name "one two"`. However, not every command's window-selector parser accepts quoted names; simple single-word names are easiest to use consistently. Avoid names matching window operations or their abbreviations: the `/window` dispatcher checks operations before trying a window name.

The status expando `%R` shows the name when set, otherwise the user-visible reference number. Renaming or clearing the name requests a status update. It does not change the window's channels, queries, server, visibility, contents, or input selection, and sends no IRC request.

## Queries, clearing, and errors

Without a name argument, the operation reports `The name of window <number> is <name>`. This is a query, not a request to clear the name. Use `name -` or `-name` to clear it; clearing normally produces no confirmation message. An empty quoted name is not an explicit unset operation, so use the hyphen form to return reliably to an unnamed window.

A numeric name is rejected with `You cannot name your window a number, that could confuse your script.` A name held by another window is rejected with `<name> is not unique!`. These refusals preserve the previous name. Successful assignment normally produces no confirmation message.

Additional words after the name are processed as subsequent window operations. A query or refused name does not stop subsequent processing, although a query ordinarily has no remaining words. Clearing with `-name` also permits later operations in the same command.

## Examples

```text
/window 3 name chat
/window chat name Chat
/window 3 name
/window 3 name -
```

Name window 3 `chat`, change only its capitalization, report its name, and clear it. A name with spaces can be assigned with:

```text
/window 3 name "server notices"
```

## Source references

- [source/window.c](../../source/window.c): `windowcmd_name` handles queries, assignment, clearing, and errors; `is_window_name_unique` and `get_window_by_desc` compare names globally; `windowcmd` checks operation names before window descriptors.
- [source/status.c](../../source/status.c): `status_refnum` implements `%R`'s name-or-number display.
- [include/words.h](../../include/words.h) and [source/ircaux.c](../../source/ircaux.c): `new_next_arg` and `universal_next_arg_count` parse the quoted name argument.
