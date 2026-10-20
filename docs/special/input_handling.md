# Input handling

## Purpose

This guide explains how a keypress becomes an editing action, command, or message. EPIC6 supports UTF-8 internally and converts terminal input from the configured console encoding. Terminal programs receive byte sequences rather than graphical key events, so the terminal emulator and EPIC must agree on both character encoding and special-key sequences.

## How EPIC handles user input

### The flow chart

1. You press a key, or paste text into the terminal.
2. The terminal emulator sends a sequence of bytes.
3. EPIC converts those bytes from the console encoding to UTF-8 and obtains a Unicode code point. An incomplete encoded character waits for more bytes; invalid input can produce an encoding diagnostic and be discarded.
4. ASCII code points enter the keybinding matcher. Code points above 127 are inserted directly into the input line, rather than looked up as custom keybindings.
5. The matcher accumulates ASCII sequences to find a binding and invokes its action. An action may edit the input line, run a script, switch windows, or submit the line.
6. Submitting the line normally offers it to `on input`, then parses it as interactive text or a command unless a hook or input prompt handles it instead.

A keypress is not necessarily one byte or one character: an arrow key usually sends an escape sequence, while a non-ASCII character can occupy several bytes in UTF-8. The character decoder and the key-sequence matcher solve different problems.

## Your terminal emulator and EPIC must agree

### Character encoding

The console encoding describes what bytes your terminal sends. EPIC initially derives it from the process locale, with a fallback if that encoding is unusable. Inspect it with `/encoding console`. For a terminal actually configured for UTF-8, select:

```irc
/encoding console UTF-8
```

Choose the terminal's real encoding rather than changing it merely to silence an error. This input setting is separate from IRC connection or target encodings. Internally, inserted text is stored as UTF-8; display width, Unicode code-point count, and byte length are not interchangeable.

### Special-key sequences

A terminal commonly sends Escape, `[`, and `D` for Left Arrow. The corresponding binding is:

```irc
/bind ^[[D backward_character
```

Here `^[` denotes Escape. EPIC's default bindings include this sequence, but a terminal may send a different one and scripts can replace bindings. Check [default keybindings](default_keybindings.md) and [bind](../commands/bind.md) when a key inserts unexpected text or performs the wrong action.

EPIC also initializes Page Up, Page Down, Home, End, Insert, and Delete bindings from the current terminal description when their capabilities are available. The terminal description does not automatically supply every possible editing key. A terminal emulator or multiplexer can consume a shortcut before EPIC receives it.

### Keybindings regroup sequences

The matcher can keep a prefix pending while waiting to see which longer sequence arrives. When the same prefix is both an action and the start of another binding, [key_interval](../set/key_interval.md), in milliseconds, controls when the shorter action can run. Unrecognized sequences can be reconsidered as shorter bound pieces; unbound characters can be discarded. Do not assume every unrecognized escape sequence will appear literally on the line.

For matched actions, [on keybinding](../on/keybinding.md) runs before the built-in action or script callback and can suppress it. The direct insertion path for code points above 127 bypasses this matcher and hook. [quote_character](../bind/quote_character.md) likewise makes the next decoded character insert directly instead of executing its normal binding.

## Editing and submitting a line

[self_insert](../bind/self_insert.md) inserts text at the cursor, or replaces the character there when [insert_mode](../set/insert_mode.md) is off. Editing actions update the local input buffer; they do not themselves send its contents to IRC.

Carriage return and line feed are normally bound to [send_line](../bind/send_line.md). That action copies and clears the input buffer before dispatch. With a normal line-input prompt active, the text goes to the prompt's callback. Otherwise [on input](../on/input.md) can intercept it, and the default interactive parser handles the line if permitted.

Ordinary interactive text goes to the current window's target, preferring a query over a channel. A leading `/` executes a command. The ordinary interactive path does not generally expand `$` forms as a script statement would; commands can still perform their own evaluation. For example, typing `/echo hello` and pressing Enter prints locally, whereas typing `hello` normally sends a message to the current target. A suitable server connection and target are needed for delivery, and the server can reject the request.

A single-key input prompt consumes a decoded key before normal binding lookup. A pause prompt can observe a key while allowing normal processing to continue. Scripts that replace bindings or intercept submitted input can therefore change the visible flow. Pasting supplies input to these same decoding and binding paths; it is not automatically a separate safe or rate-limited operation. See [paste](../scripts/paste.md) and [floodprot](../scripts/floodprot.md) for optional script behavior.

## Source references

- `source/screen.c`: console-byte conversion, `user_input_codepoint()`, and prompt routing.
- `source/recode.c`: initial console encoding and recoding rules.
- `source/keys.c`: sequence matching, timeouts, keybinding hooks, and default/terminal bindings.
- `source/input.c`: Unicode insertion and `send_line()`.
- `source/commands.c`: interactive statement and message dispatch.
- `source/window.c`: query/channel target selection.
