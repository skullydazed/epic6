# getopt function

## Synopsis

`$getopt(<optopt variable> <optarg variable> <option list> [<argument list>])`

## Technical

Parses short option flags from an argument list, returning one option per call and writing information into two named variables. Use it as the expression of a [/while](../commands/while.md) loop. The variable-name arguments use normal function word parsing; the option list and argument list always recognize double-quoted words.

Each character in the option list permits that option, case-sensitively. A following `:` requires an argument; `::` permits an optional argument. Options without colons take no argument. Use ordinary ASCII option letters.

The parser accepts grouped flags such as `-abc`. For an option taking an argument, the remainder of the same word is its argument (`-bvalue`); otherwise the next word is consumed. A following `-other` word can be consumed as that argument rather than recognized as another option. Optional arguments are also consumed from the next word when available; `::` does not restrict them to attached text.

Ordinary positional words are collected while option scanning continues. A standalone `--` ends option scanning and leaves the remaining text as positional arguments. A standalone `-` supplies an empty argument to a pending option: it counts as missing for a required argument and as an omitted argument for an optional one. Without a pending option, that standalone `-` is discarded.

## Parsing state

The argument list is fully parsed on the first call. Subsequent calls with exactly the same complete input return successive options. Changing any input text, including variable names, spacing, the option list, or argument values, starts a new parse. Keep the call's input stable throughout the loop.

When all options have been returned, a final call returns empty, clears the option variable, stores the positional remainder in the argument variable, and resets the parser. Calling again with the same input then starts over. There is only one shared parser state: nested or interleaved uses can replace each other's progress, and stopping early leaves unfinished state.

Variable assignment follows normal [/assign](../commands/assign.md) scope rules. Predeclare local variables if the results should remain local to an alias.

## Returns

| Return | Option variable | Argument variable |
| --- | --- | --- |
| Option character | That character | Its argument, or empty for a flag without one. |
| `-` | Option character | Empty; a required argument was missing. |
| `!` | Unrecognized option character | Empty. |
| Empty string at completion | Empty | Collected non-option arguments. |

Missing any of the first three required arguments returns empty without the normal completion assignments. An internal inconsistent option record can also return empty with a diagnostic. Normal unknown-option and missing-argument results do not themselves print errors.

## Restrictions

Quoted multiword option arguments are parsed as one input word, but their quotes are removed before storage and the stored arguments are later split as ordinary words. Such an argument can be truncated to its first word and shift later arguments or the positional remainder. Use single-word option arguments for reliable results.

Use standalone `--` for termination. Long options are not implemented: a word beginning with `--` is treated as termination, its prefix is stripped, and its remaining text can join directly to the following text. Positional arguments are not a faithfully preserved word list; quotes and spacing can change, and trailing spaces may remain.

## Practical and examples

An alias accepting `-a`, `-b value`, and `-c value`:

```irc
alias myalias {
    local option optopt optarg
    while (option = getopt(optopt optarg "ab:c:" $*)) {
        switch ($option) {
            (a) {echo Option $optopt used}
            (b) {echo Option $optopt used with $optarg}
            (c) {echo Option $optopt used with $optarg}
            (!) {echo Invalid option: $optopt}
            (-) {echo Missing argument for option: $optopt}
        }
    }
    echo Remaining arguments: $optarg
}
```

`myalias -a -b hello file` reports `a`, then `b` with `hello`, and leaves `file` in the remainder. `myalias -b` reports the missing-argument marker. The loop continues through those markers so the parser reaches its reset call.

## Source

Registration and the complete stateful parser: [source/functions.c](../../source/functions.c). Variable assignment and scope: [source/alias.c](../../source/alias.c). Ordinary and quoted-word parsing: [include/words.h](../../include/words.h) and [source/ircaux.c](../../source/ircaux.c).
