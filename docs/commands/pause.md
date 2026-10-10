# pause

Suspend the current script while EPIC continues processing events.

## Synopsis

```text
/pause [seconds]
```

## Description

`pause` performs a nonblocking sleep for the requested number of seconds: the calling script waits, while the client continues handling input, server traffic, timers, and other events through its event loop. Decimal intervals such as `0.5` are supported. Only the first whitespace-delimited argument is used; additional text is ignored. There are no options, and the command is always registered.

The interval is converted with `atof`, rather than validated. A nonnumeric argument becomes zero; zero and finite negative intervals schedule an already-due timer and normally resume at the next timer-processing opportunity. Use a finite nonnegative number for intentional timing.

Without an argument, `pause` waits for a decoded input character instead of a timer. It temporarily installs an empty prompt, then restores the previous input line when input arrives. The character is also processed normally: it can be inserted, trigger a binding, or submit the restored line. Arrow and other escape sequences can resume the pause on their first decoded character. This mode requires interactive fullscreen input; it cannot receive that key event through the non-fullscreen input path.

## Timing and state

`pause` does not guarantee prompt return when the interval expires. A nested command or event handler can keep the event loop occupied, and quitting the client prevents the script from resuming. Commands issued during the pause can change state: servers may disconnect, windows or input may change, and background operations may finish. Check the state you depend on after resuming.

Use [timer](timer.md) to schedule a later command without keeping the calling script suspended. Timers also depend on event-loop scheduling and do not provide a real-time execution guarantee. Do not use a fixed pause as a substitute for [wait](wait.md) or a reply hook when you need to know whether a server request has completed.

For asynchronous prompting that delivers a response to a callback, see [input](input.md) and [input_char](input_char.md). Unlike `input_char`, an argumentless pause does not consume the character exclusively for its callback. A `return` executed in an unrelated callback is not the completion mechanism for the suspended pause; its own timer or key callback marks it done.

The command produces no result value or completion message and does not itself require an IRC connection.

## Examples

Wait approximately half a second before continuing:

```text
/pause 0.5
/echo continuing
```

Wait for input, allowing that input to have its usual effect:

```text
/echo Press a key to continue
/pause
```

## History and source

`pause` first appeared in EPIC3.003; subsecond intervals were introduced in EPIC4-1.1.2.

Registration, `e_pause`, and its completion callbacks are in [source/commands.c](../../source/commands.c). Timer scheduling and dispatch are in [source/timer.c](../../source/timer.c), recursive event-loop processing in [source/irc.c](../../source/irc.c), and pass-through key prompts and input restoration in [source/screen.c](../../source/screen.c).
