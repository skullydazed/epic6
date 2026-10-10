# license

Display EPIC's embedded original copyright notices and license text.

## Synopsis

```text
/license
```

## Description

`license` prints the original license text stored in the client binary. The output includes copyright notices, conditions concerning source and binary redistribution and use of author names, and the warranty and liability disclaimer. Consult the printed text and [COPYRIGHT](../../COPYRIGHT) for the actual terms.

This is a local display command: it does not contact an IRC server or read a license file at runtime. It works without a server connection. The embedded text is fixed rather than a dynamically assembled list of every component's notices; [info](info.md) displays additional contributor credits.

## Restrictions and example

There are no options or required arguments. Any supplied arguments are ignored. The built-in is always registered and has no special platform requirements.

```text
/license
```

## Source

The registration and complete printed text are in [source/commands.c](../../source/commands.c), in the `license` handler.
