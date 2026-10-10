# local

Declare or update local variables in the current execution scope.

## Synopsis

```text
/local
/local name[,name...] [value]
/local structure.
/local -dump
```

## Description

`local name` creates a local variable with an empty value, or resets an existing visible local to empty. Supplying a value assigns the remaining command text as its value. A comma-separated name list gives every listed variable that same value; do not put spaces around the commas. Names are case-insensitive and support bracket-to-dot structure notation.

A local is visible within its atomic scope, such as an alias or hook invocation, and is removed when that scope ends. Unnamed execution frames can also see locals in enclosing frames up to the enclosing named scope. If a local of the same name is already visible, `local` updates it where it exists; it does not necessarily create a new shadowing variable in the current frame. Otherwise it creates one in the current frame. A visible local takes precedence over a global variable of the same name, leaving the global value untouched.

`local structure.` creates a structure marker. Subsequent lookups or assignments of names beneath that prefix can implicitly create locals in the marker's frame. It does not copy existing global structure values into local variables. Normal [assign](assign.md) operations can then update those locals.

With no arguments, `local` prints a listing headed `Visible Local Assigns:`. The listing walks the current frame and its parent chain and groups dotted names as structures. This diagnostic listing can include parent frames beyond the boundary used by ordinary local lookup; it is not a guarantee that every displayed name is accessible in the current expression context.

## Clearing and restrictions

`local -dump` removes local variable values from the current frame, leaving enclosing frames and global variables intact. The implementation recognizes any first argument beginning with `-d`, case-insensitively, as this operation; use the full spelling to avoid surprises.

There is no supported `local -name` deletion form. Other hyphen-prefixed names are passed to name validation and normally produce an invalid-name diagnostic. Invalid names in a comma list do not prevent later names from being attempted. Braces do not form an alternative multi-name declaration syntax, and values are plain command text rather than automatically evaluated expressions. Use an expression assignment when evaluation is wanted.

The command normally reports successful assignments, including empty values. `-dump` has no assignment confirmation. It is always registered and has no special platform requirements. Loops such as [fe](fe.md), [fec](fec.md), and [foreach](foreach.md) also assign local loop variables implicitly.

## Examples

```text
local blah this is a test
local blah,foo local vars are neat
local empty
local options.
@ options.enabled = 1
local
```

Use `local -dump` only when you intend to clear all local values in the current frame.

## History and source

`local` first appeared in EPIC4pre0.009.

Registration is in [source/commands.c](../../source/commands.c). `localcmd`, variable validation, lookup boundaries, implicit structures, listing, and clearing are in [source/alias.c](../../source/alias.c). Bracket normalization is in [source/ircaux.c](../../source/ircaux.c).
