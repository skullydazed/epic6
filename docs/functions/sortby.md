# $sortby()

## Synopsis

`$sortby(<function name> <list>)`

`$sortby({<comparison commands>} <list>)`

## Technical

Sorts a word list using a comparison function or inline command block. The comparator receives two values and must return a negative integer when the first belongs before the second, zero when they compare equal, or a positive integer when the first belongs after the second.

A leading `{` selects an inline block, extracted with normal nested-brace matching. It must be the first input character; leading whitespace prevents block recognition. Otherwise the comparator name is read as a double-quoted word, independently of `/xdebug dword`, and invoked through normal function dispatch. It can name a built-in or user-defined function, including explicit namespace prefixes.

Each comparison supplies the two words wrapped in double quotes. Inline blocks receive these as their positional argument text and return through [/return](../commands/return.md). Named functions receive the generated argument string through normal function argument expansion. Use explicit `dword` parameters in a user alias to receive grouped values reliably. The generated quotes do not escape embedded quotes or backslashes in list values; such values can be parsed differently, and named-function arguments undergo further expansion.

The returned comparator string is converted with `strtoimax(..., 0)` and narrowed to a C `int`. Empty or nonnumeric results become zero, numeric prefixes are accepted, and base prefixes are recognized. Return small values such as `-1`, `0`, and `1` rather than large integers whose narrowing can change the sign.

## Practical

Use this for ordering beyond [$sort()](sort.md)'s case-insensitive lexical order or [$numsort()](numsort.md)'s integer-portion comparison. A comparator may transform the values before comparing them; the sorted output still contains the original list words.

The list uses normal function word grouping controlled by [/xdebug dword](../commands/xdebug.md). Output is rebuilt with single spaces between nonempty words and quoting for grouped values containing literal spaces. Original separator spacing and quote spelling are not preserved. Duplicates remain, while empty words are omitted. A source variable is not assigned back automatically.

## Returns

The sorted list, or empty when no list words are supplied. Missing input returns empty. An invalid leading block returns empty and prints syntax diagnostics. An unknown comparator can return empty on each comparison, causing its values to compare equal rather than producing a separate sort error.

## Restrictions

The sort is unstable: equal comparisons have no guaranteed relative order. Callback order and number of calls are unspecified. Comparators must define a consistent ordering and should avoid side effects that alter later comparison results.

Do not call `$sortby()` recursively from its comparator. Comparator code and block mode are stored globally without saving the outer invocation's values; a nested nonempty sort overwrites and frees that state, breaking subsequent outer comparisons. There is no IRC connection requirement, but comparator code can perform its own commands and effects.

## Examples

```irc
alias compare_words (left dword, right dword) {
    return $stricmp("$left" "$right");
}
```

`$sortby(compare_words pear apple banana)` returns `apple banana pear`.

`$sortby(stricmp pear apple banana)` uses the built-in [$stricmp()](stricmp.md) directly and returns the same list for these simple values.

Registration, `function_sortby()`, and callback dispatch: [source/functions.c](../../source/functions.c). Inline function execution: [source/commands.c](../../source/commands.c). List parsing and reconstruction: [source/ircaux.c](../../source/ircaux.c). A working custom-comparator use: [script/nicklist](../../script/nicklist).
