# Lastlog

Do you mean:

- The [lastlog command](../commands/lastlog.md), `/lastlog [options] [pattern] [number [skip]]`, which searches retained output and redisplays matching lines or appends them to a file?
- The [lastlog function](../functions/lastlog.md), `$lastlog(window pattern levels...)`, which returns matching line positions rather than displaying the text?

Related settings are [set lastlog](../set/lastlog.md) and [window lastlog](../window/lastlog.md), which control retention limits, and [lastlog_level](../set/lastlog_level.md), which controls which output levels are retained. Display scrollback and searchable lastlog records are related but are not identical; see [memory usage](../special/memory_usage.md).

These interfaces use locally retained output. They do not query the server for older messages or recover records already discarded. Their pattern syntax, results, and options differ; consult the selected interface before reusing arguments from the other.

## Source references

- `source/lastlog.c`: retention, command search/display, and function results.
- `source/vars.c` and `source/window.c`: global and per-window retention settings.
