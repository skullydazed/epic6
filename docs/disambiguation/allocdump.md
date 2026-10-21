# Allocdump: unavailable diagnostic

The historical `/allocdump filename` command wrote a heap-allocation log for developers when the client was built with `ALLOC_DEBUG`. It first appeared in EPIC4-1.1.3.

The current EPIC6 source does not register `allocdump` and contains no `ALLOC_DEBUG` implementation to enable. There is no bundled script replacement or current filename, output, or error-handling contract for this command. Defining the old macro does not restore the missing handler.

This documents current availability; the repository evidence does not establish an explicit retirement date. For understanding retained output and other causes of memory growth, see [memory usage](../special/memory_usage.md). That guide is not an allocation-dump replacement.

## Source references

- `source/commands.c`: current command registry has no `allocdump` entry.
- `source/functions.c`: current function registry provides no replacement of that name.
- `configure.ac` and `include/config.h`: current build configuration has no `ALLOC_DEBUG` facility.
