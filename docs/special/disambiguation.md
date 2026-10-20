# Disambiguation: log

Do you mean the **[log command](../commands/log.md)** or the **[log function](../functions/log.md)**?

| Form | Meaning |
| --- | --- |
| `/log ...` | Create and manage independent logfiles that copy selected display output to disk. With no arguments, list registered logfiles. |
| `$log(number)` | Return the natural logarithm of a number. This mathematical function does not read, create, or control a logfile. |
| `/set log on` | Enable session logging through the [log setting](../set/log.md), using the configured session logfile. |
| `/window log on` | Enable logging for the selected window through [window log](../window/log.md). |

The two logging controls in the final rows are separate from the independent logs managed by `/log`. The shared word does not imply shared arguments, defaults, return values, or error handling. Follow the appropriate page for those details.

For example, `/echo $log(1)` displays `0`; it does not start logging. `/log` displays the independent-log list rather than evaluating a mathematical expression. These are local client interfaces and do not request an IRC server operation by themselves.

## Source references

- [source/logfiles.c](../../source/logfiles.c): `logcmd` manages independent logfiles.
- [source/functions.c](../../source/functions.c): registers `log` as a mathematical wrapper around the C library's natural logarithm.
- [source/vars.c](../../source/vars.c): session logging setting.
- [source/window.c](../../source/window.c): per-window logging control.
