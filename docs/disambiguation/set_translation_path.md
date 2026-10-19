# set translation_path

## Removed setting

The former setting used this syntax:

```text
/set translation_path [<path>]
```

It selected where EPIC searched for character-translation table files loaded through the former [set translation](set_translation.md) setting.

EPIC6 does not register `translation_path` or use that translation-file search mechanism. There is no supported path, default directory, or table-loading effect attached to this name. Without script-provided handling of unknown settings, `/set translation_path /some/directory` reports `No such variable "TRANSLATION_PATH"`. Remove obsolete assignments from startup scripts.

A script accepting the same name would supply its own behavior rather than restore the former translation tables. Changing [load_path](../set/load_path.md) does not restore this interface either; that setting concerns script file searches.

## Current encoding controls

[Encoding](../commands/encoding.md) selects console, script, server, and target encodings using the local iconv-compatible conversion library and UTF-8 conversion paths. Available encoding names depend on that library, rather than table files in an EPIC translation directory. Use an encoding rule appropriate to the actual data instead of specifying a translation-table path.

For example, if the terminal uses UTF-8:

```text
/encoding console UTF-8
```

This describes terminal input and output; it does not configure script searches or select every remote message's encoding. See [set translation](set_translation.md) for the distinction from the retired table mechanism.

## History

`set translation_path` first appeared in EPIC4-1.1.1.

## Source references

- [source/vars.c](../../source/vars.c): current registration omits this setting and handles unknown-variable errors.
- [source/recode.c](../../source/recode.c): current encoding rules and iconv-based conversion validation, without a translation-table search path.
