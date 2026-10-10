# setenv

## Synopsis

```text
/setenv <var-name> [<value>]
/setenv -<var-name>
```

## Description

`setenv` sets an environment variable in the EPIC process. The new value is inherited by processes subsequently started with [exec](exec.md). The hyphen form deletes the variable, so it is absent from those processes' inherited environment.

Changes do not alter the environment of EPIC's launching shell or processes already running. Programs can also replace or modify their inherited environment themselves. Changing an environment variable does not automatically repeat EPIC's startup configuration or change an internal [set](set.md) setting; it affects code that reads that environment variable afterward.

## Arguments and effects

The first whitespace-delimited word is the variable name. Names are passed unchanged to the operating system and are case-sensitive. The remaining text becomes the value, including embedded spaces; the command does not remove quotes or interpret `NAME=value` assignment syntax. Use `/setenv NAME value`.

A name without a value sets it to an empty string. This differs from deleting it. There is no query or environment-listing form: `/setenv` alone prints usage. [getenv](../functions/getenv.md) can read an environment variable from a script.

A leading hyphen deletes the variable only when no value remains. If a value follows `-NAME`, the command instead tries to set a variable literally named `-NAME`; use the deletion form exactly as shown in the synopsis.

The assignment path first deletes any existing value, then calls `setenv()` to install the replacement. The command ignores the operating system's return codes and prints no success or failure message. Invalid names, such as names containing `=`, or allocation failures can prevent an assignment; an unsuccessful replacement can leave the old value deleted. The command has no script return value.

## Examples

```text
/setenv EPIC_EXAMPLE ready
/setenv EPIC_EXAMPLE a value with spaces
/setenv EPIC_EXAMPLE
/setenv -EPIC_EXAMPLE
```

These set a value, replace it with text containing spaces, replace it with an empty string, and finally delete the variable.

## Source

Registration and `setenvcmd()` are in [source/commands.c](../../source/commands.c). Argument splitting is defined in [include/words.h](../../include/words.h) and implemented in [source/ircaux.c](../../source/ircaux.c) and [source/words.c](../../source/words.c). Child creation and program execution are in [source/exec.c](../../source/exec.c). The implementation calls the platform's `setenv()` and `unsetenv()` directly.
