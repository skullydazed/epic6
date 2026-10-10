# input

Prompt the user for a line, then execute commands with that response as their arguments.

## Synopsis

```text
/input [-noecho] [--] "prompt" command [arguments...]
/input [-noecho] [--] "prompt" { commands }
/input [-noecho] [--] "prompt" (argument-list) { commands }
```

## Description

`input` is primarily useful in interactive scripts, including confirmation prompts. It replaces the normal input line with the supplied prompt and waits for the user to submit a line using the send-line binding, normally Enter. The response is delivered to the supplied command or block rather than being parsed as an ordinary user command.

The command returns immediately after installing the prompt. Put work that depends on the answer inside its callback; subsequent commands in the calling script run before the answer is available. The callback receives the response as `$*`, with positional arguments such as `$0` available in the normal way. An optional parenthesized argument list before the body binds response words using EPIC's usual argument-list rules.

Use double quotes around a prompt containing spaces. Without quotes, only the first word is the prompt. A brace-delimited body supports multiple commands and defers their expansion until the answer is processed. An empty response is passed through as an empty argument string.

The prompt is attached to the current window's screen, falling back to the main screen. Its previous input line is saved and restored when the prompt is answered. Multiple prompts on the same screen form a stack: the newest is answered first, after which the older prompt resumes.

The original command's server, window, and local-variable scope are not saved with the callback. The callback runs later in the context active when the input is processed; line submission uses the then-current window's server. Do not assume locals belonging to an alias that has already returned will remain available. Arrange any required context or persistent state explicitly in the callback.

## Options

| Option | Effect |
| --- | --- |
| `-noecho` | Hide response characters while the user types. The callback still receives the full response. |
| `--` | End option parsing, useful when the prompt begins with a hyphen. |

Options must precede the prompt. Unknown leading hyphen options are silently ignored. `-noecho` only controls display: the cursor may still move, and scripts can expose the response through their own output.

## Examples

Ask for confirmation without immediately performing the action:

```text
input "Show the report? (y/n) " {
    if ([$0] == [y]) {
        echo Here is the report
    }
}
```

Bind the first response word to a named parameter:

```text
input "Your name: " (name) {
    echo Hello, $name
}
```

## Related commands and restrictions

[input_char](input_char.md) uses the same handler but completes on the next decoded input code point, without waiting for Enter. It is useful for prompts such as “press a key to continue.” It does not gather a complete line.

Interactive prompt input requires EPIC's fullscreen input handling; do not use it as a noninteractive or line-mode input mechanism. A missing prompt prints usage. A malformed callback argument list produces a syntax diagnostic when the response is processed. The built-in does not require callback text to be present; an empty callback simply consumes the response. It is always registered.

## Source

Registration, option parsing, the `input`/`input_char` distinction, and callback dispatch are in [source/commands.c](../../source/commands.c). Prompt stacking and completion are in [source/screen.c](../../source/screen.c), and line submission and echo configuration are in [source/input.c](../../source/input.c).
