# Perl: removed integration

The former Perl topics referred to an embedded-language interface:

- `perl()` evaluated Perl inside the client process.
- `perlcall()` and `perlxcall()` called subroutines in that embedded interpreter.
- A `/perl` alias could be defined to invoke the function; that spelling did not imply a separate C built-in command.

EPIC6 has retired embedded Perl. The functions are absent from the current built-in registry, configuration no longer provides the interpreter integration, and no bundled script replaces that bridge. Installing an external Perl interpreter does not restore these functions or the former `EPIC::*` callbacks.

See [using embedded Perl: removed](special_using_perl.md) for the former interface and migration context. To run a separate Perl program, use the [exec command](../commands/exec.md) with a locally installed interpreter. That is a subprocess with explicit text input/output, not shared access to the client's aliases, variables, or hooks.

## Source references

- `KNOWNBUGS`: explicit sunset of Perl support.
- `UPDATES`: project change history.
- `source/functions.c` and `source/commands.c`: current interface registries.
- `configure.ac`: current build integrations.
- `source/exec.c`: external-program alternative.
