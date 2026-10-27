# data_array script

```irc
/load data_array
```

The supplied script adds convenience aliases and functions for EPIC's named arrays. It reloads itself with the PF loader when necessary. These helpers use the array primitives that remain registered in EPIC6, including [`getitem`](../functions/getitem.md), [`setitem`](../functions/setitem.md), and [`numitems`](../functions/numitems.md).

| Helper group | Purpose |
| --- | --- |
| `itemnorm`, `getndelitems`, `getndelindex`, `getnrolitems` | Normalize item numbers, retrieve and delete items, or move items between arrays. `itemnorm` interprets -1 as the last storage item and drops out-of-range numbers. |
| `getmaskitem(s)`, `getandmitem(s)`, `getanrmitem(s)` | Retrieve, retrieve-and-delete, or move matching values. Singular forms select one random match. |
| `setnextitem(s)`, `setranditem(s)`, `setrmaxitem(s)`, `setuniqitem(s)` | Append values, replace random items, choose append versus replacement by a size threshold, or add absent values. |
| `array.purge`, `array.read`, `array.nread`, `array.stat` | Delete matching data, append file lines into arrays, or display array summaries. |
| `array.grep`/`dump` variants | Display matching values, optionally with item metadata, sorted access, or data from multiple arrays. |
| `array.write`/`flush` variants | Write array contents to files; flush variants then delete matching array data. |

The plural setters iterate over words, whereas singular setters can store the remaining text as one value. Helpers returning several values join them as text, so do not assume preservation of arbitrary value boundaries. File-writing operations open destinations for writing; inspect the script's arguments and selected arrays before using them. File reads append to existing arrays rather than clearing them first.

The source DokuWiki page was empty. This description was derived from [script/data_array](../../script/data_array), with primitive availability and behavior checked in [source/functions.c](../../source/functions.c) and [source/array.c](../../source/array.c).
