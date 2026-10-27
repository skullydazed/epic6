# Getting UTF-8 input and output working in EPIC

Correct input and output require agreement between EPIC and every terminal-processing layer between it and you. Start with the troubleshooting table below if you already know your intended encoding; read the setup sections for the underlying configuration.

## Common problems

| Symptom | Check and remedy |
| --- | --- |
| Non-English input corrupts the input line or cursor placement. | Check the terminal, multiplexer window, locale, and EPIC console rule. An encoding mismatch is one possible cause; missing glyphs and width differences can also affect display. |
| EPIC reports `Your /ENCODING CONSOLE is ... which is wrong.` | The input converter encountered a byte sequence invalid for the selected encoding. Match the console rule to the actual input bytes; do not blindly select a suggested encoding just to hide the diagnostic. |
| EPIC reports `... but you seem to be typing UTF-8.` | EPIC's heuristic detected UTF-8-like input while the console rule names another encoding. If the terminal really uses UTF-8, use `/encoding console UTF-8` and correct the startup locale. |
| Setting a locale outside a multiplexer did not change EPIC's behavior. | Inspect the environment in the shell that actually launches EPIC. Existing processes do not acquire later shell environment changes automatically; start a new appropriately configured session or set the environment before launching a new client. |
| A locale or encoding name is rejected. | Use `locale -a` for installed locale names. Encoding names accepted by `/encoding` depend separately on iconv support and EPIC's conversion checks. |

## Terminal emulator

Configure the terminal emulator to send and display UTF-8 when possible. Its encoding mode is separate from font choice: a font needs the required glyphs, but selecting a font does not universally select the terminal's byte encoding.

If UTF-8 is not appropriate for your environment, identify the actual encoding, such as ISO-8859-15, KOI8-R, or CP437, and configure the other layers consistently. Font names may offer clues on older terminals, but they are not a general source of truth for modern terminal encoding settings.

## Shell locale

List installed locales on the machine where EPIC runs:

```sh
locale -a
```

Choose an available locale whose encoding matches the terminal-facing environment. For example, if installed:

```sh
export LC_ALL=en_US.UTF-8
```

Locale names are system-specific; do not assume this exact spelling is available everywhere. Set the environment in the startup file read by your shell before launching EPIC. The current client calls `setlocale(LC_ALL, "")` at startup and obtains its console codeset through `nl_langinfo(CODESET)`. It also restores `LC_NUMERIC` to `C` for numeric parsing.

The original document said that failing to set `LC_ALL` always meant ISO-8859-1. More precisely, EPIC uses the active locale's codeset and falls back to ISO-8859-1 if its usability check fails; `LC_ALL` is not the only locale environment variable that can influence the active locale.

## GNU Screen

Screen has terminal-side and window-side encoding state. For a UTF-8 terminal, `screen -U` selects UTF-8 mode and the default encoding for new windows. Put `defutf8 on` in `~/.screenrc` to set the default for future windows; this does not update existing ones. For the current window, the Screen command `utf8 on` selects UTF-8. These distinctions are documented in the [GNU Screen manual](https://www.gnu.org/software/screen/manual/screen.html).

With Screen's usual Control-A prefix, enter its command line with Control-A followed by `:`. The original troubleshooting commands were:

```text
info
utf8 on
```

Use `info` to inspect the current window's status. The original examples contrasted a `UTF-8` indication with a legacy charset designation such as `G0[BBBB]`; exact status formatting depends on Screen's version and configuration.

Match the encoding selected for the Screen window to what EPIC expects. Configuring Screen does not universally set or rewrite locale variables in every shell. Check the environment inside that specific window rather than assuming it matches the outer shell or that it must always be set twice.

A non-UTF-8 setup is also possible when every layer agrees. For example, `LC_ALL=en_US.ISO8859-1` is meaningful only if that locale is installed and the terminal-facing input/output path actually uses ISO-8859-1.

## EPIC's console rule

Inspect the rules with [`encoding`](../commands/encoding.md). To explicitly select UTF-8 for an already-running client:

```irc
/encoding console UTF-8
```

This changes EPIC's conversion rule; it does not change the terminal or Screen mode. A legacy setup can instead select its actual console encoding, for example `/encoding console KOI8-R` when that is what the surrounding terminal path uses.

The UTF-8 warning in the input code is a heuristic, not definitive encoding detection. The “which is wrong” diagnostic follows an iconv invalid-sequence error. Fix the mismatch rather than assuming either warning proves which external layer is misconfigured.

See [encodings and locales](encodings_and_locales.md) for more background, including the distinct rules for peer messages and script files.

## Source references

- [source/irc.c](../../source/irc.c): startup locale initialization.
- [source/recode.c](../../source/recode.c): console codeset selection, fallback, encoding validation, and rule changes.
- [source/screen.c](../../source/screen.c): `translate_user_input()` conversion and the two console-encoding diagnostics.
