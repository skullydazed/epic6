# Repeat

Do you mean:

- The [repeat command](../commands/repeat.md), `/repeat count { commands }`, which executes command text repeatedly?
- The [repeat function](../functions/repeat.md), `$repeat(count text)`, which returns copies of the text concatenated without a separator and does not execute it?

For example, `/repeat 3 { echo hello; }` executes three echoes, while `$repeat(3 hello)` returns `hellohellohello`. The command also accepts a parenthesized count expression; the function uses its own decimal count parser. Their parsing and control-flow rules are different.

Neither interface introduces a delay by itself. For scheduled work, see [timer](../commands/timer.md). Use modest counts: command repetitions can monopolize execution, and string repetitions allocate memory proportional to the requested output. See the selected page for errors, limits, and loop-control behavior.

## Source references

- `source/if.c`: repeat-command execution.
- `source/functions.c`: string repetition and function registration.
- `source/commands.c`: command registration.
