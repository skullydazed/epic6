# set

## Synopsis

```text
/set
/set <variable>
/set <variable> <value>
/set -<variable>
```

## Description

`set` displays or changes EPIC's internal settings. These settings control client behavior such as text display, input, logging, and protocol handling. This is different from creating script variables with [assign](assign.md) or [local](local.md).

With no name, `set` displays all registered settings and their values. With a name but no value, it displays the current value. Names are case-insensitive and may be abbreviated to an unambiguous prefix. An exact name takes priority over longer names sharing that prefix. An ambiguous prefix prints a diagnostic and the matching settings without changing them; an unknown name normally prints `No such variable`. Matching is by prefix, not a wildcard pattern.

Supplying a value changes the selected setting and normally prints `New value of NAME is VALUE`. A query prints `Current value of NAME is VALUE`; an unset string is shown as `<EMPTY>`. There is no script return value.

## Value types

Each registered setting has one of four types:

| Type | Accepted value | Effect of `/set -name` |
| --- | --- | --- |
| Boolean | `on`, `off`, or `toggle`, case-insensitive. Only the first value word is used. | Leaves the value unchanged and displays it. |
| Integer | A numeric word whose converted value is nonnegative. Only the first value word is used. | Leaves the value unchanged and displays it. |
| Character | The first decoded Unicode character, which must occupy exactly one display column. Additional characters are ignored. | Sets the character to a space. |
| String | The entire remaining text, including spaces. | Clears the string, giving it no value. |

Integer validation accepts digits, optionally preceded by `-`, then uses C's base-detecting integer conversion. Avoid leading zeroes: `010` becomes 8, and an invalid octal digit can stop conversion early. Hexadecimal notation and a leading `+` fail the initial numeric check. Individual settings may impose additional limits through their callbacks.

For strings, `set` consumes exactly one separator character after the name; additional spaces belong to the value. Quotes are also literal parts of a string value, rather than delimiters to remove. For example, `/set continued_line  ` with two trailing spaces sets a one-space continuation marker. An empty value is a query; use the hyphen form to clear a string. Values supplied after `-name` are ignored.

The hyphen form clears a value according to its type; it does **not** restore the setting's startup default. Defaults and effects vary by setting. Many are initially defined in the build configuration and may subsequently be changed by startup scripts.

## Hooks and setting effects

Before handling a named setting, EPIC offers the request to [on](on.md) `set` handlers. The hook receives the uppercased name entered and the value; a hyphen request is represented by `<unset>`. For an abbreviation, it also offers the resolved full name if the first hook allows processing to continue. A handler can suppress the built-in operation. Unknown names additionally go through `on unknown_set`; errors are offered to `on set` with `set-error` text. A bare `/set` listing does not go through these hooks.

Accepted changes invoke the setting's C callback or registered script callback, when present. These may normalize the value, report additional errors, or change related state; the final displayed value reflects the resulting setting. Changing a setting to its existing value still invokes its callback. Recursion protection prevents the same setting's hooks and callbacks from repeatedly invoking themselves.

Settings are client configuration, but their effects need not be limited to a stored global value. For example, `/set indent` applies its value to every existing window. Check the individual setting's documentation when its scope or restrictions matter.

Scripts can register typed settings through [symbolctl](../functions/symbolctl.md), or implement requests using hooks. Registered script settings appear in the same list and use the same type rules. An arbitrary `assign` does not become a setting, and this command's registry lookup does not automatically load stubbed aliases or variables.

## Examples

```text
/set
/set beep
/set beep off
/set beep toggle
/set continued_line >
/set -continued_line
/set pad_char .
```

The first two commands display settings; the next two disable and then toggle the beep setting. The remaining commands set a continuation marker, clear it, and choose a one-column padding character.

## Source

Registration is in [source/commands.c](../../source/commands.c). Parsing, type validation, output, callbacks, and built-in setting registration are in [source/vars.c](../../source/vars.c). Registry prefix matching and script-created settings are in [source/alias.c](../../source/alias.c). Numeric conversion and Unicode helpers are in [source/ircaux.c](../../source/ircaux.c); initial defaults are also defined in [include/config.h](../../include/config.h).
