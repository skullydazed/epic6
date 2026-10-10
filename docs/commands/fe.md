# fe

Loop through a word list, optionally processing several words at a time or rewriting the list.

## Synopsis

```text
/fe (list) variable [variable ...] { commands }
/fe source_variable variable [variable ...] { commands }
```

## Description

In the first form, `fe` expands the parenthesized list once, then assigns successive words to the loop variables and executes the command block. The list can combine literal text, variables, and function expansions. With three loop variables, each iteration consumes three words. If the last group is incomplete, unused variables receive empty strings. An empty list executes no iterations.

Words are separated by whitespace. Double-quoted words containing spaces are treated as one item when `/xdebug dword` is enabled; disabling that setting disables this extended-word parsing. Loop variables are [local](local.md) variables: existing visible locals are updated, or locals are created in the current scope. The loop does not create a separate scope or restore their old values afterward. The body receives the enclosing command's argument context; loop items are accessed through their named variables.

In the second form, supply the source variable's name without `$` or parentheses. Its initial value supplies the word list. After each iteration, the current values of the loop variables are appended to a replacement value, separated by spaces. The source variable is assigned that replacement after the loop ends, using ordinary [assign](assign.md) scope rules. Changing a loop variable inside the body therefore changes the replacement list. Empty values are omitted, and values containing spaces are not automatically quoted, so this form does not preserve the original spacing or quoted-word boundaries.

[break](break.md) stops the loop after the current variables have been appended in the rewriting form; the unprocessed remainder is discarded. [continue](continue.md) also appends the current values before moving to the next group. A return or another propagated control-flow exception stops the loop after that same append step. An initially empty source variable is left untouched.

## Examples

Process names in groups of three, issuing one mode request for each group:

```text
fe ($friends) xx yy zz {
    if (zz != []) {
        mode #blah +ooo $xx $yy $zz
    } elsif (yy != []) {
        mode #blah +oo $xx $yy
    } else {
        mode #blah +o $xx
    }
}
```

The server decides whether these mode changes are allowed.

Square each number in a variable's word list:

```text
@ list = [2 3 4]
fe list number {
    @ number = number * number
}
echo $list
```

The resulting list is `4 9 16`.

## Related commands and restrictions

[fec](fec.md) shares the handler but iterates through Unicode code points instead of words and concatenates replacement values without spaces. It is not a word-loop alias. Other loop commands include [for](for.md) and [foreach](foreach.md).

At least one loop variable and a brace-delimited body are required; an empty body is allowed. Put whitespace before the opening brace. The implementation accepts at most 255 loop variables and diagnoses too many variables, a missing list, a missing body, or a missing closing brace. Use valid variable names. There are no command options, and this built-in is always registered.

## History and source

`fe` and `fec` have used local loop variables since January 22, 1997.

Registration is in [source/commands.c](../../source/commands.c), and the command-name branches, iteration, mapping, and control flow are in [source/if.c](../../source/if.c). Word parsing is defined in [include/words.h](../../include/words.h), [source/words.c](../../source/words.c), and [source/ircaux.c](../../source/ircaux.c); variable assignment is in [source/alias.c](../../source/alias.c).
