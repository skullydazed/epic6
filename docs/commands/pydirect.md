# pydirect

Call an importable Python function with one text argument.

## Synopsis

```text
/pydirect module.function [text]
```

## Technical

`pydirect` imports the named module, retrieves the named attribute, checks that it is callable, and calls it with exactly one Python string argument containing the remaining command text. It does not parse that text as Python code, evaluate expressions within it, or divide it into separate Python arguments. With no remaining text, the callable receives an empty string. Ordinary EPIC command expansion can occur before the handler receives the text.

The first token supports the command parser's double-quoted argument handling, but the module and attribute names must still be valid for Python import and lookup. The name is split at its first dot: `module.function` means import `module` and look up the literal attribute `function`. Additional dots are part of that attribute name, rather than a traversal of submodules or nested objects. Use [python](python.md) for more complex expressions, argument lists, or dotted access. Python module and attribute names are case-sensitive.

The interpreter must already be initialized. The current direct-call path does not initialize it or check that prerequisite itself. Run `/python pass` before the first direct call, or otherwise initialize it through the Python statement/expression interface. Those interfaces share the embedded interpreter and its persistent state; `pydirect` does not create a fresh interpreter for each call.

## Returns

The callable's return value is converted with Python `repr`, but this command discards the resulting string and produces no success output. Use the [pydirect function](../functions/pydirect.md) when you need the result. The called function can produce its own output and side effects.

With no callable token, the command prints `Usage: /PYDIRECT module.method arguments`. A name without a dot produces the same usage diagnostic. Import failure, a missing attribute, or a noncallable attribute produces a specific diagnostic. Call failures and return-value representation failures use the Python exception-reporting path, which prints `The python evaluation threw an exception.` and invokes Python's exception printer. Do not assume that every failure produces a full traceback in an EPIC window.

## Restrictions

The command exists only in builds with `HAVE_PYTHON`. Configure optionally detects Python embedding support through `python3-config`, using its embedding flags when available and a compile/link check. `--with-python` requests support, `--with-python=/path/to/python3-config` selects a configuration executable, and `--without-python` disables it. The project's configure requirements describe Python 3.8 or later; the embedding headers and libraries must be available at build time.

The module must be importable through the embedded interpreter's Python import path, and the callable must accept the one-string calling convention. Execution is synchronous in the client: a long-running or blocking callable delays normal client processing. The command itself needs no IRC connection, though called code may use EPIC APIs that do.

## Examples

Initialize Python, then display a string through the embedded EPIC module:

```text
/python pass
/pydirect _epic.echo Hello from Python
```

The callable receives `Hello from Python` as one string. The `_epic.echo` implementation displays it in EPIC; the command discards that callable's numeric return value.

## Source

Conditional registration is in [source/commands.c](../../source/commands.c). `pydirect_cmd`, `call_python_directly`, interpreter initialization, exception reporting, and `_epic.echo` are in [source/python.c](../../source/python.c). Python detection and build options are in [configure.ac](../../configure.ac).
