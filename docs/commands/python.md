# python

Execute Python code in EPIC's embedded interpreter.

## Synopsis

```text
/python code
/python {code}
```

## Technical

`python` executes the supplied text as Python code, including statements such as assignments and imports. Expression statements can run too, but their values are not automatically displayed. Use the [python function](../functions/python.md) to evaluate an expression and obtain its represented value.

On first use, the command initializes the embedded interpreter and registers the `_epic` module. Subsequent calls share the interpreter's persistent `__main__` namespace, so variables, definitions, and imports remain available. `/python pass` initializes it without defining anything. An empty code body also reaches the interpreter; there is no missing-code usage diagnostic or option parser.

If the received text begins with `{`, EPIC extracts the balanced outer braced body and executes its contents. Text after the closing brace is ignored by this handler. An unmatched opening brace reports `PYTHON: unbalanced {` and executes nothing. Braces are EPIC grouping syntax here; Python indentation and syntax still apply inside the body. An initial Python dictionary literal can therefore need a surrounding statement or an extra outer block to avoid being mistaken for EPIC grouping.

Ordinary EPIC parsing and expansion can affect the code before the handler receives it, especially in aliases and loaded scripts. The handler itself adds no further variable expansion. Take account of both languages' quoting and escaping rules when embedding Python in EPIC scripts.

## Practical

Import `_epic` to call the client bridge. `_epic.echo(text)` displays text unconditionally; `_epic.say(text)` uses normal banner/display behavior. `_epic.cmd(text)` runs a supplied EPIC statement without expanding its dollar expressions, while `_epic.eval(text)` runs EPIC code with expansion and an empty argument context. These calls can perform real client or server actions. Other bridge methods provide expressions, functions, variables, and file-descriptor callbacks.

Python `print()` uses Python's standard output; this integration does not redirect standard output or standard error to EPIC windows. Use `_epic.echo` or `_epic.say` for client-window output.

## Returns

This command discards evaluation results and returns no value to the script. Output and side effects come from the executed code. On failure, Python's statement runner reports the exception, then EPIC invokes its exception-reporting helper, which announces `The python evaluation threw an exception.` and calls Python's exception printer. A full traceback is not guaranteed to appear in an EPIC window. Statements completed before an exception are not rolled back.

## Restrictions

The command is registered only when the build defines `HAVE_PYTHON`. Configure uses `python3-config` and a compile/link test to detect embedding support. `--with-python` requests it explicitly, `--with-python=/path/to/python3-config` selects the configuration executable, and `--without-python` disables it. The project describes Python 3.8 or later as its requirement; embedding headers and libraries must be available when building.

Python runs synchronously in the client process, so blocking or lengthy code delays normal event processing. The command itself needs no IRC connection; called bridge methods may need a connected server or other client context. Imported modules follow the embedded interpreter's Python import path, rather than EPIC's script load path.

[pydirect](pydirect.md) calls a module's callable with one text argument without parsing that argument as Python code. Its current direct-call path requires prior interpreter initialization, which this command provides.

## Examples

Initialize Python and show a message in EPIC:

```text
/python import _epic
/python _epic.echo("Hello from Python")
```

Store a value and use it in a later call:

```text
/python answer = 6 * 7
/python _epic.echo(str(answer))
```

The second call displays `42`. These calls share the previously imported `_epic` module.

A braced body can group a short Python program:

```text
/python {import _epic; _epic.echo("Grouped code")}
```

## Source

Conditional registration is in [source/commands.c](../../source/commands.c). `pythoncmd`, interpreter initialization, statement/expression execution, exception reporting, and the `_epic` bridge are in [source/python.c](../../source/python.c). Configure detection and build options are in [configure.ac](../../configure.ac).
