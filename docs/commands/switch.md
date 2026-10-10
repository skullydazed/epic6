# switch

## Synopsis

```text
switch (<control-text>) {
    (<pattern>) {<commands>}
    (<pattern>) (<alternative-pattern>) {<commands>}
    (*) {<fallback-commands>}
}
```

## Description

`switch` chooses among several alternatives by matching a string, avoiding a chain of [if](if.md) conditions. The control text and case labels undergo normal variable, positional-argument, and function expansion. They are strings, not arithmetic expressions: `switch (1 + 1)` compares the text `1 + 1`, rather than the number 2. Use an explicit expression expansion when a computed value is needed.

Each case consists of one or more parenthesized patterns followed by a braced command body. EPIC uses its case-insensitive wildcard matcher, not regular expressions or a best-match ranking. Literal patterns match the whole string; `*` matches any sequence and `?` matches one character. Multiple labels before the same body are alternatives: any matching label selects that body.

Only the **first matching case group** executes. There is no fallthrough and no `default` keyword. Put general patterns last; a final `(*)` body provides a fallback. Without a matching case, nothing executes. The fallback is optional, as is an empty overall body.

## Expansion and control flow

The control text is expanded once. Case labels are expanded as groups are reached; all labels in a reached group are expanded, even after one of them matches. Later groups are not visited after a match. Only the selected command body is executed and expanded as ordinary script commands. Labels containing function calls can therefore have side effects, even when their case body is not chosen.

The selected body runs in the current script scope with the same argument context. [break](break.md) exits that body and is consumed by this switch, allowing execution after the switch to continue. A break is not required at the end of a case. [return](return.md) propagates to its enclosing command or function context. [continue](continue.md) can propagate to an enclosing loop; a switch alone does not provide a continue target.

## Syntax and restrictions

Parentheses and braces are required. Leading and trailing whitespace within the delimited control text and labels is trimmed before expansion; whitespace between the syntax groups is permitted. Spaces inside the resulting strings remain significant for matching. Several labels and bodies can appear on one script line or across lines supported by the script loader. Separate commands within a body using ordinary script syntax.

Malformed grouping produces syntax diagnostics, such as a missing control string, execution body, case label, or case body. EPIC parses cases while searching rather than validating every branch in advance, so an unvisited later error can remain unnoticed. Text after the overall switch body is not another case and is ignored by the handler; put subsequent commands outside the switch using normal statement separators.

There are no options, automatic output, or script return value. The command is always registered and does not require an IRC connection. Selected commands can produce output and other effects.

## Example

```text
alias choose {
    switch ($0) {
        (foo) {echo selected foo}
        (bar) (blah) {echo selected bar or blah}
        (*) {echo selected something else}
    }
}
```

`/choose foo` selects the first body, `/choose BLAH` selects the second, and an unmatched argument selects the fallback. Each invocation runs only one body.

## Source

Registration is in [source/commands.c](../../source/commands.c). `switchcmd()` and delimiter parsing are in [source/if.c](../../source/if.c). Expansion and variable lookup are in [source/alias.c](../../source/alias.c), wildcard matching in [source/reg.c](../../source/reg.c), and command-block execution and control-flow exceptions in [source/commands.c](../../source/commands.c).
