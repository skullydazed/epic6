# set translation

## Removed setting

The former setting used this syntax:

```text
/set translation <character-translation-table>
```

It selected a byte translation table between the client's Latin-1 character representation and terminal-specific character sets. Historical tables included eight-bit sets such as `CP437`, `CP850`, `MACINTOSH`, and `DEC_MCS`, and seven-bit national replacement sets such as `DANISH`, `FRENCH`, `GERMAN`, and `UNITED_KINGDOM`.

EPIC6 does not register `translation` as a setting or provide that table-selection interface. There is no supported table value or default under this name. The old assumption that the client internally uses Latin-1 does not describe its current UTF-8 conversion paths.

Without script-provided handling of unknown settings, `/set translation CP437` reports `No such variable "TRANSLATION"`. Remove obsolete assignments from startup scripts. A script accepting this name would define its own behavior rather than restore the former byte tables.

## Current encoding controls

Use [encoding](../commands/encoding.md) to describe terminal, script, server, or target encodings. This uses the system's iconv-compatible conversion support and UTF-8 internally, rather than the old fixed translation-table names. Supported encoding names depend on the local conversion library; historical translation names are not guaranteed to be accepted or equivalent.

For a terminal actually configured to use UTF-8, the console rule can be set explicitly:

```text
/encoding console UTF-8
```

The console rule describes the bytes typed and displayed by your terminal. Remote message and script encodings have separate rules; changing the console rule does not describe every server or correspondent. Prefer a correct locale and rules matching the actual encoding rather than an arbitrary table intended to change a character's appearance.

## History

`set translation` was introduced before ircII2.2, removed in EPIC4pre0.009, and reintroduced in EPIC4-1.1.1. That historical interface is absent from EPIC6.

## Source references

- [source/vars.c](../../source/vars.c): current setting registration omits `translation` and handles unknown-variable errors.
- [source/recode.c](../../source/recode.c): `encoding`, console/script/network rules, UTF-8 conversions, and validation against the local iconv implementation.
