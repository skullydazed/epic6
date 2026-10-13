# python function

## Synopsis

`$python(<Python expression>)`

## Technical

Evaluates the entire argument string as a Python expression in the embedded interpreter. The handler does not split words or remove quotes; ordinary EPIC function argument expansion happens before Python sees the text. Use Python string literals when the expression needs string values.

The interpreter is initialized automatically on first use. Evaluation uses the persistent `__main__` namespace as both globals and locals, shared with the [/python command](../commands/python.md). Variables and imports established by that command are available to later expressions. The function evaluates expressions, including function calls; ordinary assignment and import statements belong in the command interface.

## Practical

Use Python calculations or calls where an EPIC script needs a returned value. [$pydirect()](pydirect.md) provides a simpler way to pass one text argument to an importable Python callable without composing a Python expression.

The embedded `_epic` module is available for explicit import and provides access to client operations. Python code runs synchronously and can have side effects; a long-running expression delays normal client processing.

## Returns

On success, the result object's Python `repr()` converted to UTF-8. Strings include representation quotes and escapes, booleans become `True` or `False`, and Python `None` becomes `None`. This is a representation, not automatic conversion to an EPIC word list or unquoted string.

An evaluation exception prints `The python evaluation threw an exception.` and invokes Python's exception printer. The wrapper then continues into result conversion rather than returning a checked failure value. CPython 3.13 represents the null result pointer as `<NULL>`; this is not an EPIC-defined error sentinel. Empty input is an invalid Python expression and follows the exception path. Errors in representation or UTF-8 conversion are unchecked and can lead to invalid pointer handling rather than a reliable return value. See [CPython's object representation implementation](https://github.com/python/cpython/blob/3.13/Objects/object.c) for the null-result behavior.

## Restrictions

Registered only in builds with `HAVE_PYTHON`. Configure detects Python embedding headers and libraries through `python3-config`; `--with-python` requests support, `--with-python=/path/to/python3-config` selects that executable, and `--without-python` disables support. No IRC connection is required by the wrapper itself.

## Examples

| Expression | Result |
| --- | --- |
| `$python(2 + 3)` | `5` |
| `$python('hello'.upper())` | `'HELLO'` |
| `$python(1 < 2)` | `True` |
| `$python(None)` | `None` |

After `/python answer = 42`, `$python(answer + 1)` returns `43` using the shared namespace.

## Source

Conditional registration and `function_python()`: [source/functions.c](../../source/functions.c). `python_eval_expression()`, initialization, statement evaluation, `_epic`, and exception reporting: [source/python.c](../../source/python.c). Build configuration: [configure.ac](../../configure.ac).
