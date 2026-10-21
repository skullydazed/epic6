# Using embedded Perl: removed

EPIC6 no longer includes the embedded Perl environment. The former `perl()`, `perlcall()`, and `perlxcall()` functions are not registered C built-ins, and there is no configure option that restores that interpreter.

## What the former interface provided

The embedded interface evaluated Perl inside the client process. `perl()` initialized the interpreter and evaluated expressions; `perlcall()` called a defined Perl subroutine in scalar context, while `perlxcall()` supported more complex argument exchange. The interpreter exposed `EPIC::cmd`, `EPIC::eval`, `EPIC::expr`, and `EPIC::yell` so Perl code could execute client commands, evaluate expressions, or report messages.

Those entry points, shared interpreter state, and callbacks are unavailable in EPIC6. Examples that define hooks or aliases calling them do not establish a working Perl bridge. There is no current embedded-Perl return-value, error-handling, signal-handler, or networking contract. A script can define functions with similar names, but that does not restore the removed C implementation.

## Running an external Perl program

[exec](../commands/exec.md) remains available for running a separate program asynchronously. If Perl is installed and available on the executable search path, an interactive example is:

```text
/exec -direct perl -e "print qq(hello from Perl),chr(10)"
```

This starts an external interpreter and displays its output through exec handling. It does not create an embedded interpreter or give the child access to `EPIC::*`, local client variables, aliases, or hooks. Exec provides pipes and output callbacks for explicitly exchanging text; consult its documentation for shell versus direct execution and quoting. Script statements can undergo EPIC expansion before the external program sees their arguments.

An external interpreter has its own startup cost and memory use, so no universal performance advantage is implied. If the task needs direct client hooks or variable access, implement that part using EPIC [programming](../special/programming.md) and [expressions](../special/expressions.md), with an explicit text protocol to the child where needed. The former raw-DCC networking alternative is also unavailable in EPIC6.

## Source references

- `KNOWNBUGS`: records the sunset of Perl support and DCC.
- `source/functions.c`: current function registry has no embedded Perl entry points.
- `configure.ac`: current build configuration has no embedded Perl integration.
- `source/exec.c`: external process launch, pipes, output handling, and cleanup.
