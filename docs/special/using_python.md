# Using Python in EPIC

The high-level [epic module](../../script/epic.py) exposes decorators, output helpers, expression evaluation, and socket integration for Python code running inside the IRC client. It wraps the embedded `_epic` module implemented in [source/python.c](../../source/python.c). The original guide described EPIC5; the implementation qualifications below apply to this EPIC6 tree.

## Getting started

Python support is optional at build time. The project lists Python 3.8 or later; configuration uses `python3-config` and an embedding compile/link test. `--with-python` requests support, `--with-python=/path/to/python3-config` selects the configuration executable, and `--without-python` disables it. Installing a Python interpreter afterward does not add the omitted client interfaces.

In a Python-enabled client, initialize the high-level environment with:

```irc
/load python
```

For startup use, put `load python` in `~/.epicrc`, or load `global`, which already includes it. The helper initializes Python, adds the current directory and its own script directory to `sys.path` when needed, imports `epic`, and defines the development aliases below. It does not automatically import the module's names into every Python file: import the names you use.

Save your Python code in an importable module, such as `my_script.py`, then use `/pyload my_script`. Importing the module executes its decorators and other top-level code. The module must be discoverable through `sys.path`.

## Quick examples

```python
from epic import alias, on, say, expression, NOISE_QUIET

@alias('epic_command')
def epic_command(args):
    """An EPIC command backed by Python."""
    say('Saying ' + args)
    say('Commit ID: ' + expression('info(i)'))

@on('connect', noise_indicator=NOISE_QUIET)
def on_connect(args):
    """Observe accepted IRC registration."""
    say('Connected: ' + args)
```

After importing that module, `/epic_command hello` calls the Python function with the single string `'hello'`. `info(i)` reports the internal commit ID, not the release version. CONNECT runs after accepted IRC registration, not merely after establishing a TCP socket.

## EPIC for Python programmers

EPIC descends from ircII and supports an interactive terminal interface, script-defined commands, and event handlers. Script packs can customize the user experience. It can also run event-driven automation, optionally inside a terminal multiplexer so you can inspect client state interactively. These facilities do not guarantee that all networks expose identical features or that every possible event has a hook.

Python code normally enters through user commands, registered events, or descriptor callbacks. EPIC owns the event loop: Python callbacks run synchronously, so blocking work or long-running request handlers delay IRC processing too.

### User commands

The input line accepts the client's scripting language, for example:

```irc
/fe (#epic #python) channel {join $channel}
```

Use `@alias()` to expose Python functions as commands. `/alias` lists aliases, and `/alias <name>` shows a definition, including the small PYDIRECT shim installed by the decorator.

### Events

Use `@on()` to register callbacks for available events, such as [connect](../on/connect.md), joins, and incoming messages. `/on` lists installed hooks. The hook's own reference defines its arguments and timing; a handler receives their combined text as one Python string and must parse it as needed.

### Socket listeners

Register a listening descriptor with `register_listener_callback()` or use `SocketServerMixin`. EPIC dispatches readiness through its existing event loop; do not start a separate blocking `serve_forever()` loop inside an ordinary client callback.

## Logging and output

Importing `epic` installs a handler on the logger named `epic`, with an INFO-level console handler. Use that logger or one of its children:

```python
import logging

logger = logging.getLogger('epic.my_script')
logger.info('Hello, World!')
```

The original `logging.getLogger('my_script')` example did not inherit this handler merely by importing `epic`. Other logger configuration can change propagation and filtering.

The module also replaces Python's `sys.stdout` and `sys.stderr` with `Console` objects that send text through EPIC's `echo`, labeled `PYTHON-STDOUT` or `PYTHON-STDERR`. This captures Python writes through those objects, not every native-library or operating-system descriptor write. Each write strips trailing whitespace and splits lines; it is not a byte-preserving terminal stream.

## Classes

### SocketServerMixin

Place the mixin before a compatible **server class**, not before `socketserver.BaseRequestHandler`. For example, the bundled [shortener example](../../script/shortener.py) combines it with `http.server.HTTPServer`:

```python
from http.server import HTTPServer
from epic import SocketServerMixin

class EPICHTTPServer(SocketServerMixin, HTTPServer):
    pass
```

Provide an appropriate request-handler class when constructing the server. The mixin registers the server's listening socket, handles one ready request through `_handle_request_noblock()`, calls `service_actions()`, and cancels the callback before closing the server. A synchronous request handler can still block EPIC; the mixin does not make its application code asynchronous.

### Console

`Console` is the file-like output adapter used for Python standard output, standard error, and the module's logging handler. Its `write()` sends lines to EPIC and its `flush()` does nothing; it is not a complete replacement for every file API.

## Decorators

### alias(name)

An alias callback receives exactly one string containing the text after the command. Command-style invocation ignores the Python return value; use logging, `echo()`, `say()`, or `xecho()` to display a result.

```python
from epic import alias, xecho

@alias('hello')
def hello(args):
    xecho('Hello, %s!' % args)
```

Typing `/hello Python World` calls `hello('Python World')`. `/alias hello` shows the installed shim, which invokes the module-level function through PYDIRECT. Use importable module-level functions; PYDIRECT splits its callable name at the first dot rather than traversing arbitrary dotted objects.

### on(event_type, wildcard_pattern='*', noise_indicator=NOISE_SILENT, ...)

The decorator installs an ON shim invoking the function with one event-argument string. The default serial selector is `'-'`, which allocates an unused negative serial. Optional arguments are `exclude_match`, `delete`, `serial_number`, and `flexible_pattern`; consult the implementation and [on](../commands/on.md) for their matching and replacement effects.

A corrected version of the original incoming-message example is:

```python
from epic import on, xecho, NOISE_QUIET

@on('general_privmsg', '*enemy ships*', NOISE_QUIET)
def enemy_ships(args):
    xecho("It's a trap!")
```

The original used `privmsg`, which is not the current incoming hook name. GENERAL_PRIVMSG supplies sender, destination, and text after CTCP processing; its pattern matches the entire combined argument string, so this example does not restrict the phrase to the message body alone. CTCP-only messages can be consumed before this hook. Inspect the shim with `/on general_privmsg`.

The module's noise constants are `NOISE_DEFAULT` (`''`), `NOISE_SILENT` (`'^'`), `NOISE_QUIET` (`'-'`), `NOISE_NOISY` (`'+'`), and `NOISE_SYSTEM` (`'%'`). Noise affects hook output and default handling according to ON rules. In particular, the suppressing noise modes do not cancel default handling merely by being used at the decorator's default negative serial; that decision belongs to serial zero.

Importing a module again normally uses Python's import cache. Reloading it reruns decorators; automatically allocated serials can leave additional hook registrations rather than replacing old ones. Do not assume reload is a complete cleanup of earlier client state.

## Calling EPIC from Python

### xecho()

`xecho(message, ...)` constructs an XECHO command and normally adds the configured banner. The helper offers all-window/server, current/visible/window, expiry, level/target, logging, raw-output, and display options. See its actual signature and implementation: some keyword arguments are forwarded only when they have the expected Python type, and the helper does not implement every conceivable XECHO option. It returns `None` because the wrapper has no explicit return.

```python
from epic import xecho
xecho("It's a trap!")
```

### command() and evaluate()

`command` is the high-level name for `_epic.cmd`; `evaluate` names `_epic.eval`. Both run client commands, but they treat the supplied text differently:

| Helper | Behavior |
| --- | --- |
| `command(text)` | Run a single command statement with the supplied argument text, without first expanding its dollar expressions or treating its semicolons as an outer command block. |
| `evaluate(text)` | Run an ircII command block with ordinary expansion; its positional `$*` context is empty. |

Prefer `command()` when you intend literal argument data. It still invokes the chosen command or alias, whose own parser may interpret that data; it is not a blanket guarantee that arbitrary untrusted text is safe as executable client input.

Contrary to the original guide, these two bridge functions return the Python integer **0** after dispatch, not `None`. That value does not prove that a server accepted the requested operation.

### expression()

`expression(text)` evaluates an ircII expression and returns its result as a Python string. Functions and variable names in this expression do not require the input-line `$` prefix:

```python
from epic import expression

logging_enabled = expression('LOG') == 'ON'
chanops = expression('chops(#epic)')
servers = expression('myservers()')
```

Even numeric-looking expression results are strings. Function requirements and loaded alias overrides still apply; this does not turn EPIC expressions into Python expressions.

### register_listener_callback()

```text
register_listener_callback(fd, dispatch_function, cleanup_function)
```

Pass an integer listening descriptor, a dispatch callback accepting the descriptor, and a cleanup callback. The helper returns `True` after registration, or displays a warning and returns `False` if its tracking dictionary already contains the descriptor. It registers readability with flags zero and retains the callbacks in that dictionary. The module's EXIT hook cancels tracked callbacks and calls their cleanup functions without arguments; cleanup callbacks must support that call form.

`cancel_callback(fd)` removes EPIC's I/O registration but does not itself close the Python-owned descriptor. The high-level tracking dictionary is not pruned by cancellation or the mixin's close method, so a closed descriptor number can remain marked as registered. Manage server objects and callback lifetimes accordingly. Callback exceptions enter the bridge's error/cleanup path; cleanup failure can disable that descriptor. No Python service loop or automatic application protocol is supplied.

## Calling Python from EPIC

### /python

The [python command](../commands/python.md) executes Python code in the persistent `__main__` namespace:

```irc
/python answer = 6 * 7
/python print(answer)
```

It accepts expression statements too, but discards their values. The original assertion that an expression necessarily causes an exception was incorrect. Ordinary Python syntax and exceptions still apply, and completed side effects are not rolled back on failure.

### $python()

The [python function](../functions/python.md) evaluates one Python expression in that shared namespace and returns its `repr()` text. **Strings include representation quotes and escapes**, correcting the original claim that strings are returned bare:

```text
$python(answer + 1)       -> 43
$python('hello'.upper()) -> 'HELLO'
```

The expression interface's current error/result-conversion path is incompletely checked; do not rely on a stable error sentinel after failed evaluation or representation. Use the command/function references for the precise limitations.

### Development aliases

The `python` loader defines:

| Alias | Meaning |
| --- | --- |
| `/pyecho <expression>` | Echo `$python()`'s representation result. |
| `/pyload <module>` | Execute `import <module>`. |
| `/pyreload <module>` | Call `epic.reload(<module>)`; the module object must already be bound in `__main__`, as it is after `/pyload module`. |

Reload updates the imported module but does not automatically remove prior hooks, aliases, listeners, or other side effects.

## Further documentation and historical contact

See the [EPIC help collection](help_root.md) for the wider scripting and client interface. The original guide invited programmers to EFnet at `irc.efnet.net`, channel `#epic`; that is retained as historical contact information rather than a current availability claim.

## Acknowledgements

The original credited hop with EPIC5 and its low-level Python integration, and skullY with this guide and the high-level module. It thanked hop for the work and discussion involved in embedding Python, and caf and the other `#epic` participants for feedback that improved the integration.

## Source validation

- [script/epic.py](../../script/epic.py): high-level exports, decorators, output/logger setup, mixin, and listener registry.
- [script/python](../../script/python) and [script/global](../../script/global): initialization, import paths, development aliases, and default startup loading.
- [source/python.c](../../source/python.c): embedded module methods, dispatch return values, expression representation, persistent namespace, and descriptor callbacks/cancellation.
- [source/commands.c](../../source/commands.c), [source/functions.c](../../source/functions.c), and [source/hook.c](../../source/hook.c): conditional Python registration, referenced expression functions, and current event names/serial rules.
- [configure.ac](../../configure.ac): optional Python detection and embedding requirements.
