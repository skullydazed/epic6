# window list

## Synopsis

```text
/window [<window>] list
```

## Description

Display a table of all windows known to the client, including visible windows on every screen and hidden windows on every server. A window selector does not filter the table. The header and each window row are printed as local output; no IRC request is sent.

| Column | Meaning |
| --- | --- |
| Ref | Window reference number. |
| Nick | Your nickname on the window's server, not the window's query target. |
| Name | Window name, or `<None>` if unnamed. |
| Channel | The window's current channel, or `<None>`; this is not a list of every channel assigned to the window. |
| Query | The window's current query target, or `<None>`. |
| Server | The server's reported name. |
| Level | The window's output-routing [level](level.md) mask. |

Hidden windows have `Hidden` appended after the level mask. The table does not identify the current window with a separate marker or show each window's screen number.

## Formatting and arguments

Nicknames are limited to 12 bytes, query targets to 9, and server names to 10 in this display; these are byte-based formatting limits rather than Unicode display-width limits. The name column is at least 6 characters wide and expands to the longest window name. [set channel_name_width](../set/channel_name_width.md) controls the channel column; a value of zero uses 12 here. Truncated fields do not change the underlying names or settings. Use [window describe](describe.md) for more detail about an individual window.

There are no arguments or options specific to `list`. Additional words are processed as subsequent window operations. `/window -list` is a no-op. Listing does not change input selection, window visibility, sizes, or routing masks.

## Example

```text
/window list
```

Print the complete window table. `/window 3 list` prints the same set of windows; it does not restrict the list to window 3 or its server.

## Source references

- [source/window.c](../../source/window.c): `windowcmd_list` calculates column widths and prints the header; `list_a_window` and `WIN_FORM` define row fields, truncation, and the hidden marker. `traverse_all_windows2` and `traverse_all_windows` enumerate the client-wide window set; `get_window_echannel` supplies the current channel.
