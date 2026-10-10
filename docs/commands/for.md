# for

Repeat commands using a condition, an integer range, or a word list.

## Synopsis

```text
/for (once, condition, each) { commands }
/for variable from first to last { commands }
/for variable from first to last step increment { commands }
/for variable in (word list) { commands }
```

## Standard form

The parenthesized form resembles a C `for` statement, but uses commas between its three components. `once` and `each` are command blocks; `condition` is an expression using the same truth rules as [if](if.md).

EPIC executes `once` before the loop. It then evaluates `condition` before each iteration. A false result stops the loop, possibly before the first iteration. After the body completes, EPIC executes `each` and tests the condition again. Semicolons can separate multiple commands inside `once` or `each`.

`once`, `each`, and the body may be empty, but the commas, parentheses, and body braces are required. An empty condition evaluates false; use `1` for an unconditional loop. The header is split at its first two literal commas, so commas inside expressions, function calls, or command arguments can interfere with this syntax.

[continue](continue.md) in the body proceeds to `each` before the next condition test. [break](break.md) exits without running `each`. A return or another propagated control-flow exception also stops the loop. Place loop-control commands in the body; the initialization runs before this loop begins catching them.

## Integer range form

The range form assigns successive integers to `variable` and runs the body for each value. Both endpoints are inclusive when reached. The default increment is `1`; a descending range requires a negative increment. A positive increment with `first > last`, or a negative increment with `first < last`, executes no iterations.

The endpoints and increment are parsed as decimal integers, not evaluated as expressions. Use literal integers or values expanded before this command is parsed. Changing the loop variable in the body does not change the internal counter or the next value assigned. `continue` advances to the next value; `break` exits immediately. Return and other propagated exceptions also stop iteration.

The spelling `variable = first to last` is also accepted. Use the documented `to` and `step` keywords even though the current parser does not validate those particular tokens. Always use a nonzero increment: zero can produce an infinite loop when the starting value is greater than or equal to the endpoint. Keep ranges within the platform's integer limits.

## Word-list form

The list form expands the parenthesized word list once, assigns each word to `variable`, and executes the body. An empty expanded list executes no iterations. Whitespace separates words; double-quoted words containing spaces are honored when `/xdebug dword` is enabled. `continue`, `break`, and propagated exceptions behave as in the range form. Changing the variable does not alter the remaining list.

A nonparenthesized `in` pattern is reserved for future use and produces an error; it is not wildcard iteration.

## Variables and restrictions

Range and list variables are [local](local.md): an existing visible local is updated, or one is created in the current scope. These forms do not introduce a separate scope or restore the previous value afterward. Assignments in the standard form follow the normal rules of the commands used; they are not automatically local. All bodies receive the enclosing argument context.

Missing components, malformed parentheses or braces, and incorrect range/list argument counts produce diagnostics. There are no options or special platform requirements, and the built-in is always registered. The command does not return a value or print output by itself.

## Examples

Display a countdown three times:

```text
for (@ xx = 3, xx > 0, @ xx--) {
    echo WARNING! This ship will self destruct in $xx seconds!
}
```

Display `1`, `3`, and `5`:

```text
for ii from 1 to 5 step 2 {
    echo $ii
}
```

Display a descending range:

```text
for ii from 3 to 1 step -1 {
    echo $ii
}
```

Display each word:

```text
for xx in (one two three) {
    echo $xx
}
```

## Related commands and source

See [fe](fe.md), [foreach](foreach.md), [while](while.md), and [do](do.md).

Registration is in [source/commands.c](../../source/commands.c). `forcmd`, the range and list helpers, and argument validation are in [source/if.c](../../source/if.c). Expression evaluation is in [source/expr.c](../../source/expr.c) and [source/expr2.c](../../source/expr2.c); truth testing and argument splitting are in [source/ircaux.c](../../source/ircaux.c). Local assignment is in [source/alias.c](../../source/alias.c), and word parsing is defined in [include/words.h](../../include/words.h) and [source/words.c](../../source/words.c).
