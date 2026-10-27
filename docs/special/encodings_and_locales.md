# Encodings and locales

## Character encodings

A character encoding maps characters to stored or transmitted bytes. A terminal also needs to interpret those bytes and display suitable glyphs. Bytes, characters, key presses, and display columns are different things; they do not generally have a one-to-one relationship.

Legacy single-byte encodings provide at most 256 byte values and assign different meanings to some of the same values. For example:

- `CP437` includes the box-drawing characters used by many older scripts.
- `ISO-8859-1` represents characters used in several Western European languages.
- `KOI8-R` represents Russian text.

Interpreting bytes with the wrong encoding can produce box graphics where letters were intended, or letters where graphics were intended. A script encoded in CP437 and conversation encoded in KOI8-R need separate conversion rules rather than a single shared interpretation.

Unicode assigns code points to characters. UTF-8 encodes a Unicode scalar value in one to four bytes, preserving ASCII byte values for ASCII characters. The original document's “one to six bytes” description is not the modern UTF-8 encoding model. A code point's byte count also does not determine its terminal width: combining marks and wide characters are examples of the distinction.

## EPIC and iconv

EPIC6 uses UTF-8 internally and `iconv` for conversion at its input/output boundaries. Available encoding names depend on the system's iconv implementation, and EPIC checks whether the requested conversions are usable. Encodings that produce embedded NUL bytes are unsuitable for its string interfaces; not every encoding accepted by iconv is appropriate for every EPIC use.

On systems supporting this option, list iconv encodings with:

```sh
iconv --list
```

EPIC initializes its console encoding from `nl_langinfo(CODESET)`, which reflects the active locale. If that encoding fails its usability check, it tries `ISO-8859-1`. Separate default rules handle non-UTF-8 scripts (`CP437`) and otherwise-unmatched non-UTF-8 IRC text (`ISO-8859-1`). Valid incoming UTF-8 is accepted directly. Use [`encoding`](../commands/encoding.md) to inspect or change the applicable rules, including an explicit console rule when necessary:

```irc
/encoding console UTF-8
```

This must match the encoding actually used by the terminal.

## Locales

A locale communicates language, formatting, and character-classification conventions to software. Names often follow `language_COUNTRY.encoding`, but spelling and availability depend on the operating system. List installed locales with:

```sh
locale -a
```

Examples from the original document illustrate the naming convention; they are not a promise that each locale is installed:

| Locale name | Meaning |
| --- | --- |
| `en_US.ISO8859-15` | US English with ISO-8859-15. |
| `en_US.UTF-8` | US English with UTF-8. |
| `fi_FI.ISO8859-1` | Finnish with ISO-8859-1. |
| `fi_FI.UTF-8` | Finnish with UTF-8. |
| `ja_JP.SJIS` | Japanese with Shift-JIS. |
| `ru_RU.KOI8-R` | Russian with KOI8-R. |
| `ru_RU.CP1251` | Russian with CP1251. |
| `ru_RU.UTF-8` | Russian with UTF-8. |

For a shell environment using an installed UTF-8 locale, the original example is:

```sh
export LC_ALL=en_US.UTF-8
```

Set an appropriate installed locale in the startup file read by your shell before launching EPIC. EPIC calls `setlocale(LC_ALL, "")` at startup, then deliberately restores `LC_NUMERIC` to `C` for numeric parsing. Setting a locale communicates an encoding expectation; it does not change the bytes emitted by a terminal or convert every program's data automatically.

## Terminals, multiplexers, and fonts

The terminal emulator, any multiplexer, the environment used to start them, and EPIC's console rule need to agree. If an existing multiplexer session retains an old environment, merely detaching and reattaching may not apply a shell's new locale; start a new session with the intended environment when needed. The original page's reports comparing GNU Screen and tmux were historical troubleshooting observations, not current guarantees about either program.

Terminal settings can select an encoding independently of locale variables. Fonts also need glyphs for the characters being displayed; a correct UTF-8 configuration cannot make a missing glyph appear. Check terminal configuration and font coverage when conversion settings are correct but output still looks wrong.

## See also

The locale and console rule describe your own input/output environment. The [`encoding`](../commands/encoding.md) command also supplies rules for peers, channels, servers, and scripts that use different encodings.

## Source references

- [source/irc.c](../../source/irc.c): startup locale initialization and the numeric-locale override.
- [source/recode.c](../../source/recode.c): `init_recodings()`, iconv validation, console/script/IRC rules, and incoming UTF-8 handling.
- [source/screen.c](../../source/screen.c) and [source/wcwidth.c](../../source/wcwidth.c): Unicode-aware display preparation and column widths.
