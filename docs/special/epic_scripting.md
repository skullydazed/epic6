# An introduction to scripting with EPIC

This guide introduces EPIC6 scripting for readers who have some programming experience. It covers the basic language model, events, aliases, variables, and flow control. It begins the learning process rather than specifying every parser rule; the linked reference pages provide the details.

## The ircII language

EPIC descends from ircII and retains its fundamental scripting model. Other ircII-derived clients historically shared much of that language, but their commands, functions, variables, and parser extensions differ. Do not assume a script written for another client will work unchanged in EPIC6.

The language has similarities to shell languages and Tcl, but its own expansion and grouping rules matter more than those analogies.

**Script values are strings.** User variables and stored code are represented as NUL-terminated C strings. Expressions can interpret those strings as numbers or other operands, but variables are not binary buffers and cannot hold embedded NUL bytes. Code can be stored and evaluated as text; aliases are interpreted when called rather than compiled into bytecode. This does not mean the language lacks flow control: commands implement its branches and loops.

**Whitespace matters.** A space separates a command name from its argument text. Each command then applies its own argument grammar. Lists frequently use whitespace-separated words, but quoted-word support depends on the parser being used. There is no universal shell-style quoting rule, and tabs and repeated spaces are not interchangeable in every string value. Check the command or function reference before assuming that quotes group its arguments or are removed.

## Statements and blocks

A block contains statements separated by semicolons. Braces group a block or protect text for a later evaluation pass, depending on the command using them.

| Statement form | Purpose |
| --- | --- |
| `command arguments` | Execute a user alias or built-in command. |
| `@ expression` | Evaluate an expression for its effects. |
| `(expression)` | Another expression-statement form. |
| `{ statements }` | Execute a nested block. |
| `(argument-list) { statements }` | Bind arguments to local variables and execute the block. |

For example, `3 + 4` calculates a value, while `count = 7` assigns one. A standalone expression statement discards its final value; use an assignment to retain it, or `${expression}` to insert an expression result into command text.

Commands such as `join` and `quit` are built into the client. Commands created with `alias` are script text registered under a name. `if` and `while` are also commands, whose argument parsers implement branching and looping. Keep whitespace between a command and its arguments: `if (condition)` is the command form, whereas `if(condition)` does not have that separation.

These examples use semicolons so their statement boundaries are explicit for the [PF loader](pf_loader.md). The [standard loader](standard_loader.md) has additional line-based preprocessing and can insert separators in ordinary multiline scripts. Newlines by themselves are not PF statement separators. See [ircII syntax](ircii_syntax.md) for expansion and grouping rules.

At the interactive prompt, prefix a command with `/`; text without it is normally sent to the current conversation target. Ordinary interactive commands also do not receive the same general expansion pass as script commands. Use [`eval`](../commands/eval.md) when you deliberately want that pass. The examples below are script text to load, unless an interactive invocation is shown explicitly.

## The event-driven model

A script normally registers definitions and callbacks, then returns control to EPIC. EPIC processes network input, keyboard input, timers, and other work, invoking the relevant callbacks when events occur. Long-running script bodies can delay that work; an event-driven model does not make every script operation asynchronous.

Without custom handlers, the client uses its available default behavior and any loaded built-in scripts. Customization usually means replacing a display action, adding an independent action, or defining commands that users invoke later.

## Handling events

Suppose a join normally appears as:

```text
*** Henry (hcutter@example.com) has joined channel #irchelp
```

You can replace that display with a timestamp and a different arrangement:

```irc
on ^join "*" (nick, chan, userhost, status) {
    xecho -b [$strftime(%H:%M)] [$chan] JOIN: $nick ($userhost);
};
```

[`on`](../commands/on.md) identifies an event, a matching pattern, an optional argument list, and a command body. It does not have the original tutorial's fixed “three arguments”: its syntax includes optional modifiers and serial numbers as well.

An event carries an argument string whose layout depends on the event. For [`join`](../on/join.md), the fields are:

| Position | Meaning |
| --- | --- |
| `$0` | Joining nickname. |
| `$1` | Channel. |
| `$2` | Prefix userhost, when present. |
| `$3-` | Optional historical compatibility status text, normally empty. |

The server must report the join before this event occurs; sending `/join` alone does not imply success. The channel state is updated before the hook runs, and suppressing its display does not undo the join.

The example's argument list places those fields in local variables before the body runs. The final named parameter receives the remaining text, so a separate `status` parameter keeps that trailing text out of `userhost`. Without an argument list, positional expandos refer to the event's argument string. Argument lists consume their fields: `$*` inside a named-argument handler is the unconsumed remainder, not necessarily the original event text. See [argument lists](arglist.md).

### Noise and default display

The caret in `^join` selects a suppressing noise mode. At the default serial number, zero, the selected handler suppresses the event's normal display and supplies its own output. `xecho` is used here to display the replacement explicitly.

Use a suppressing handler when replacing a default action, not automatically for every script hook. A quiet `-join` handler can perform additional work while allowing the default display. Ordinary noise-based suppression applies at serial zero; hooks at other serial numbers support independent processing without that same default-display decision.

### Matching and exceptions

Patterns match the complete event argument string. `"*"` matches everything, and quoting a pattern lets it include spaces. This quoted-word handling is part of `on's` grammar; it does not establish how every other command treats quotes.

A broad handler and a more specific exception can coexist at the same serial number:

```irc
on ^join "*" (nick, chan, userhost, status) {
    xecho -b [$chan] JOIN: $nick ($userhost);
};
on ^join "bob *" (nick, chan, userhost, status) {
    xecho -b HUZZAH! $nick!$userhost has joined $chan!;
};
```

Within one serial group, EPIC selects the matching pattern with the highest wildcard score. A successful match gets a point for each literal character matched and one for the terminating NUL; `"*"` scores 1, and `"bob *"` scores 5. The latter wins for Bob's joins, while the broad pattern handles other nicknames. Exact-score ties follow stored pattern order, so do not use definition order as an exception mechanism.

This selection happens separately at each serial number, so more than one callback can run for an event when separate groups are registered. Defining the same event, serial, and case-insensitive pattern again replaces that handler. See [serial numbers](serial_numbers.md) and [patterns](patterns.md) before composing larger scripts.

The handler body is ordinary script code. A brace-delimited body is the clearest form for multiple statements; keep its grouping balanced. Hooks can perform other operations besides display, but their effects remain subject to each command's rules and the event's timing.

## Defining aliases

An alias is a named script body usable as a command and, with a return value, as a function. Defining one stores its body rather than executing it:

```irc
alias MyNewAlias {
    echo Eek! You scared me!;
};
```

Invoke it interactively with `/MyNewAlias`. Command names are case-insensitive. An alias can also be called by other script code.

The body is interpreted each time it runs, and replacing the definition changes subsequent invocations immediately. Definition handling still checks grouping and argument-list syntax, so it is too broad to say that malformed code is never examined until use. Inspect or correct diagnostics when defining code as well as when running it.

Without a declared argument list, `$*` holds supplied argument text and `$0`, `$1`, and so on select its words. Named parameters make that relationship explicit:

```irc
alias greet (name, remainder) {
    echo Hello, $name. Remaining text: $remainder;
};
```

`/greet Henry welcome aboard` assigns `Henry` to `name` and `welcome aboard` to `remainder`. The last ordinary parameter takes the remaining text. End a parameter list with `...` to retain unconsumed arguments in `$*`, or `void` to discard them. See [`alias`](../commands/alias.md) for function calls, return values, and interactions with built-ins.

## Variables and scope

User variables, also called assigns, store strings. A global remains available until explicitly changed or removed, or until the client exits; it is not immutable or automatically saved across sessions.

An alias or named callback normally creates a new local scope, and its parameter variables are local. Those locals disappear when the call finishes, allowing recursive calls or unrelated scripts to reuse names. Braces alone do not create a new local scope: a local declared inside an `if` or loop normally belongs to the surrounding alias or callback.

Create locals explicitly when using temporary values:

```irc
alias count_example {
    @ :count = 0;
    @ count = count + 1;
    echo Count: $count;
};
```

An unqualified assignment updates a visible local if one exists; otherwise it can update or create a global. Use [`local`](../commands/local.md) or `:name` assignment to request local storage, and `::name` assignment to request global storage. See [atomic scopes](atomic_scope.md) for visibility across calls.

## Expressions and truth values

In expressions, numbers are constants, bare names normally refer to variables, and bracketed text such as `[hello]` is a string operand. Operators combine values; parentheses group subexpressions. Function calls can contribute values as well.

An empty result or a string wholly representing numeric zero is false. Nonzero numbers and other nonempty strings are true. Thus a variable containing `0` is false, while a nonempty nonnumeric string is true. Expression statements do not automatically print results. See [expressions](expressions.md) for operators and precedence.

## Basic flow control

### if, else, and elsif

```irc
if (count > 0) {
    echo The count is positive.;
};
```

The condition must be parenthesized. A true condition executes its body; a false one skips it. `if` also permits a single-command body, but braces make multiple statements and alternatives clear.

```irc
if (count > 0) {
    echo Positive.;
} else {
    echo Zero or negative.;
};
```

For more than two cases:

```irc
if (count > 0) {
    echo Positive.;
} elsif (count < 0) {
    echo Negative.;
} else {
    echo Zero.;
};
```

Only the first selected branch runs. Since `if` is a command, its condition and bodies follow that command's grammar; braces and whitespace should be written deliberately rather than inferred from another language. See [`if`](../commands/if.md).

### while, break, and continue

```irc
@ :count = 0;
while (count < 3) {
    echo Iteration $count;
    @ count++;
};
```

The loop tests the expression before each iteration and does not run if it is initially false. [`break`](../commands/break.md) exits an enclosing loop; [`continue`](../commands/continue.md) skips the remainder of the current iteration and retests the condition. Ensure that your loop eventually changes its condition or deliberately exits. See [`while`](../commands/while.md).

### fe: iterate over words

```irc
fe (Henry Bob Alice) person {
    echo Hello, $person.;
};
```

[`fe`](../commands/fe.md) expands its parenthesized word list once and assigns successive words to the named local variables. It can process groups using several variables:

```irc
fe (Henry red Bob blue) person color {
    echo $person chose $color.;
};
```

The current implementation accepts 1–255 variable names, correcting the original tutorial's limit of 254. An incomplete final group fills unused variables with empty strings. Existing visible locals with those names are updated, and their final values remain in the enclosing scope after the loop. `fe` does not create an additional local scope or restore prior values. `break` ends iteration; `continue` moves to the next group. Quoted-word handling follows its documented `dword` behavior.

## Wildcards and scripts

Hook and matching-function patterns use EPIC's [wildcard syntax](patterns.md), not regular expressions. `*` matches a sequence, while `%` matches within a word. The pattern is matched against the relevant text according to the particular command or function.

A startup file can load scripts, define aliases, register event handlers, and set defaults. By default, EPIC checks `~/.epicrc` and falls back to `~/.ircrc`; environment variables and command-line options can select another file or skip loading. If the selected file is unreadable, the current startup path loads `global` instead.

Startup is not the only opportunity to run code. You can load a script later with [`load`](../commands/load.md), invoke aliases from the input prompt, or arrange timers and other callbacks. Once definitions are installed, EPIC calls them as appropriate while continuing its normal client work. Changes made only in the running client do not automatically update the startup file.

## Source references

- [source/commands.c](../../source/commands.c): `parse_block()`, `parse_statement()`, alias/callback execution, command expansion, and both loaders.
- [source/alias.c](../../source/alias.c): alias definitions, argument binding, variable lookup, and local scopes.
- [source/hook.c](../../source/hook.c): event registration, wildcard selection per serial group, and noise/default-action behavior.
- [source/parse.c](../../source/parse.c): JOIN arguments, state updates, and default display.
- [source/if.c](../../source/if.c) and [source/ircaux.c](../../source/ircaux.c): flow-control commands, `fe` limits, and `check_val()` truth conversion.
- [source/irc.c](../../source/irc.c) and [include/irc.h](../../include/irc.h): startup-file selection and loading.
