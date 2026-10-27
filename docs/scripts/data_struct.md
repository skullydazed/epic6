# data_struct script

```irc
/load data_struct
```

The supplied script adds helpers for [`assign`](../commands/assign.md) variables, command aliases, and dot-separated variable structures. It selects the PF loader when necessary and uses the current [`aliasctl`](../functions/aliasctl.md) interface to inspect and manipulate symbols. These structures are distinct from EPIC's named arrays.

| Helper group | Purpose |
| --- | --- |
| `assign.check`, `assign.qcheck` and alias counterparts | Check the ordering/consistency of matching symbol lists; intended for investigating suspected internal problems. |
| `assign.dump`, `assign.grep` | Display matching variables, values, and package names, optionally filtering values. |
| `assign.pack`, `assign.purge` | Reassign matching entries or delete them. |
| `assign.save`, `assign.esave`, `assign.flush`, `assign.eflush` | Write matching definitions to a file, using ordinary or encoded output; flush variants then purge matching definitions. |
| `struct.purge`, `struct.purgesub` | Recursively delete a named structure, including or excluding its own root value. |
| `struct.savefn`, `struct.savefd` | Save nonempty values recursively to a named file or an already-open file descriptor. |
| `assign.add`, `assign.addn`, `assign.ifnul`, `assign.filter`, `assign.uniq` | Maintain word-list values, initialize empty values, filter contents, or remove repeated entries. |

The script generates `alias.*` counterparts for its matching `assign.*` helpers and reverse-matching `r` variants for the groups defined through its `alias.tt` generator, such as `assign.rdump` and `alias.rdump`. Read the definitions for exact arguments before using mutating helpers. Save operations open their destination for writing; purge and flush operations delete matching live definitions.

The script explicitly requires the `functions` and `data_array` scripts for `assign.uniq`; it does not load them automatically. Its alias save/pack paths preserve command text rather than separately preserving alias argument-list metadata, so do not treat them as lossless backups of every alias property.

The source DokuWiki page was empty. This description was derived from [script/data_struct](../../script/data_struct), with its symbol interface checked in [source/alias.c](../../source/alias.c) and the supporting scripts [script/functions](../../script/functions) and [script/data_array](../../script/data_array).
