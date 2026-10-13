# pydirect function

## Synopsis

`$pydirect(<module.callable> [text])`

## Technical

Imports a Python module, retrieves its named attribute, and calls that attribute with exactly one Python string argument containing the remaining text. The text is not evaluated as Python code or split into multiple Python arguments. With no remaining text, the callable receives an empty string. Ordinary EPIC function argument expansion occurs before the handler receives the arguments.

The callable name uses normal function word parsing, including double-quoted grouping when `/xdebug dword` is enabled. It is split at the first dot: `module.callable` imports `module` and looks up the literal attribute `callable`. Additional dots are part of that attribute name, not nested attribute or submodule traversal. Python module and attribute names are case-sensitive.

The embedded interpreter must already be initialized. This direct-call path neither initializes it nor checks that prerequisite. Run [/python pass](../commands/python.md) before the first direct call, or initialize it through the Python expression interface. Calls share the interpreter's persistent state.

## Practical

Use this function to pass text to an importable Python callable without constructing a Python expression containing that text. Use [$python()](python.md) for arbitrary Python expressions, nested attribute access, or other argument signatures. The [/pydirect command](../commands/pydirect.md) uses the same calling mechanism but discards the result.

## Returns

The Python `repr()` of the returned object, encoded as UTF-8. A returned Python string therefore includes representation quotes and any escapes, rather than just its contents. A callable returning `None` produces `None`.

Missing input returns the empty string. A name without a dot returns the empty string and a usage diagnostic. Import failure, a missing attribute, or a noncallable attribute produces a specific diagnostic and an empty result. Exceptions during the call or return conversion also return the empty string through the exception-reporting path, which prints `The python evaluation threw an exception.` and invokes Python's exception printer; a full traceback is not guaranteed to appear in an EPIC window.

## Restrictions

Registered only in builds with `HAVE_PYTHON`. Configure detects Python embedding headers and libraries through `python3-config`; `--with-python` requests support, `--with-python=/path/to/python3-config` selects that executable, and `--without-python` disables support.

The module must be importable through the embedded interpreter's import path, and the attribute must accept one string argument. Called code can produce output and other side effects. Execution is synchronous, so a blocking callable delays client processing. No IRC connection is required by the wrapper itself.

## Examples

After initializing the interpreter with `/python pass`:

| Expression | Result |
| --- | --- |
| `$pydirect(builtins.len hello there)` | `11` |
| `$pydirect(builtins.str hello)` | `'hello'` |
| `$pydirect(builtins.len)` | `0` |

## Source

Conditional registration and `function_pydirect()`: [source/functions.c](../../source/functions.c). `call_python_directly()`, initialization, and exception reporting: [source/python.c](../../source/python.c). Build configuration: [configure.ac](../../configure.ac).
