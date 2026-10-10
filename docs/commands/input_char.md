# input_char

Prompt for one input character, then run commands with that response.

## Synopsis

```text
/input_char [-noecho] [--] "prompt" command [arguments...]
/input_char [-noecho] [--] "prompt" { commands }
/input_char [-noecho] [--] "prompt" (argument-list) { commands }
```

## Description

`input_char` installs a visible prompt and returns immediately. The next decoded input code point completes the prompt without requiring Enter. EPIC converts input using the console [encoding](encoding.md), then passes the response to the callback as a UTF-8 string in `$*`. An optional parenthesized argument list binds callback parameters using ordinary EPIC argument-list rules.

Put work depending on the answer inside the callback: commands following `input_char` in the calling script run before the answer arrives. Use a brace-delimited body for multiple commands and to defer their expansion. Quote a prompt containing spaces; otherwise its first word is the prompt.

The response is one Unicode code point, which can occupy several UTF-8 bytes. It is not necessarily one displayed character or a whole terminal key sequence. Combining sequences can contain several code points. Arrow and function keys often send escape sequences: their first code point can complete this prompt, leaving subsequent input to normal processing. Enter supplies its decoded control character rather than an empty submitted line. Use `$*` to inspect the complete response; word-oriented positional arguments may not preserve a whitespace response.

The character is intercepted before normal keybinding dispatch and insertion into the input line. It therefore triggers the callback rather than its usual editing action. The previous input line is restored before the callback runs. Prompts on a screen are stacked, with the newest answered first.

The prompt uses the current window's screen, falling back to the main screen. The originating server, window, and local scope are not captured for later callback execution. Arrange persistent state and any required command context explicitly; locals from an alias that has returned are not preserved by the prompt.

## Options

`-noecho` requests hidden input, and `--` ends option parsing. Options precede the prompt; unknown leading hyphen options are discarded. In the single-character branch, the response is consumed before insertion regardless of `-noecho`, so it is not normally displayed as typed text. The option does not conceal output produced by your callback.

## Example

```text
input_char "Press 'q' to quit, any other character to continue: " {
    unless ([$*] == [q]) {
        echo Continuing
    }
}
```

This exits the callback on `q`; it does not exit EPIC.

## Related commands and restrictions

[input](input.md) waits for a submitted line instead. The `$'...'` prompt expando uses the same single-key prompt machinery but waits synchronously during expansion; `input_char` supplies an asynchronous callback.

Interactive completion requires fullscreen input handling. A missing prompt prints usage, and a malformed callback argument list produces a diagnostic when the response arrives. An empty callback is accepted and consumes the response. The built-in is always registered.

## History and source

`input_char` first appeared in EPIC3.003.

Registration, options, and callback execution are in [source/commands.c](../../source/commands.c). Console decoding, interception before keybindings, and prompt stacking are in [source/screen.c](../../source/screen.c). The synchronous prompt expando is in [source/expr.c](../../source/expr.c).
