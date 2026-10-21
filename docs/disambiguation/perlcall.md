# Perlcall: removed embedded subroutine calls

The former `$perlcall(subroutine [text])` function called a previously defined subroutine in the embedded Perl interpreter. It passed the optional text as one argument, requested scalar context, and returned the scalar result. The related [perlxcall](perlxcall.md) interface provided more control over argument and array exchange.

EPIC6 has removed embedded Perl and no longer registers `perlcall()`. There is no bundled replacement, interpreter initialization step, callable embedded-subroutine table, or current scalar/array return contract. An empty result from a missing interface must not be interpreted as the former “interpreter not started” or subroutine-error status.

The old examples that defined persistent Perl subroutines, retained Perl globals across calls, or exchanged data with EPIC arrays do not work in the current client. Installing an external Perl executable does not restore that in-process state or the former `EPIC::*` callbacks.

See [Perl: removed integration](perl.md) and [using embedded Perl: removed](special_using_perl.md). For a separate local program, use [exec](../commands/exec.md) and explicitly exchange text through its input/output facilities. Native client hooks and variables remain available through EPIC [programming](../special/programming.md), but are not automatically shared with the child process.

## Source references

- `source/functions.c`: current registry omits `perlcall` and the embedded Perl entry points.
- `configure.ac`: no embedded Perl build integration.
- `KNOWNBUGS` and `UPDATES`: retirement of Perl support.
- `source/exec.c`: separate-process alternative.
