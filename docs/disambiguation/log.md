# Log

Do you mean:

- The [log command](../commands/log.md), `/log`, which creates and manages selective logfiles for client output?
- The [log function](../functions/log.md), `$log(number)`, which calculates a natural logarithm and has nothing to do with writing files?
- The [logctl function](../functions/logctl.md), `$logctl(...)`, which inspects and controls the selective logfiles programmatically?

For other file-logging controls, see [set log](../set/log.md) for the global session log and [window log](../window/log.md) for per-window logging. These are separate mechanisms from the selective `log` command. [mangle_logfiles](../set/mangle_logfiles.md) controls formatting filters applied to logfile text.

File logging uses client output and local files; it does not request older messages from an IRC server. For searching already retained in-memory output, see [lastlog](lastlog.md). Consult the selected interface for filenames, filters, errors, and return values.

## Source references

- `source/logfiles.c`: selective logfile command and control operations.
- `source/functions.c`: natural-logarithm wrapper and `logctl` function interface.
- `source/log.c` and `source/window.c`: session and window logging.
