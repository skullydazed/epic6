# nopaste script

## Synopsis

```text
/load nopaste
/set nopaste on|off|toggle
```

## Description

This script adds the `nopaste` setting to help prevent flooding your current target by accidentally pasting several lines into the client. Turning the setting on installs an input filter; turning it off disables the filter. The default is `off`, explicitly applied when the script loads.

Protection applies when you submit ordinary input through the [input](../on/input.md) hook. A line beginning with a literal `/` bypasses the check, so pasted commands can still execute normally. Empty lines also bypass it. The filter is intended to reduce accidental text flooding; it does not make pasted commands safe or limit all outgoing IRC traffic.

## How filtering works

The script compares submission times, not individual keystrokes. Enabling protection records the current time and clears a shared counter. For each nonempty line not beginning with `/`, it:

1. Measures microseconds since the preceding checked line, or since protection was enabled.
2. Adds `(100000 - elapsed_microseconds) / line_length` to the counter, using the submitted line's Unicode code-point count.
3. Clamps a negative counter to zero.
4. Discards the line if the counter is greater than `2500`; otherwise, permits normal input processing.

The timestamp advances even for discarded lines. Fast submissions less than a tenth of a second apart increase the counter; longer pauses reduce it when another checked line arrives. Short lines increase it more quickly than long lines at the same interval. The constants are fixed in the script and have no settings.

This is a heuristic, not a fixed 400-character-per-second limit. The first pasted line is not guaranteed to pass, and the second is not guaranteed to be blocked. Enough rapid input can trigger suppression, but timing, line lengths, the existing counter, and client processing all affect the result. Waiting alone does not run a reset timer: the elapsed pause is accounted for on the next checked submission.

## Effects and limitations

A suppressed line is discarded without a warning or retry queue. The input buffer has already been cleared by submission; the script additionally invokes the `erase_line` key action. There is no saved paste to approve or send later. A permitted line continues through normal interactive parsing and can be sent to the current target, subject to the connection and server.

The filter's counter and timestamp are shared across windows, targets, and server connections. Switching to another target does not reset them. Slash-prefixed commands and empty submissions neither update the timestamp nor change the counter. The exemption is hardcoded to `/`, independent of other command-prefix configuration; leading whitespace before `/` does not take this exemption.

The script uses a serial-zero conditional `on input *` hook, so it can conflict with scripts that install the same broad hook. Disabling it runs `on input -*`, removing the serial-zero wildcard hook currently installed, without restoring a displaced handler. Direct script output, commands that bypass `on input`, and replies to an active line-input prompt are outside this filter.

The script loads [utime](utime.md) for timestamp subtraction and [addset](addset.md) for its script-provided Boolean setting. It needs no external program or optional platform feature. The `setup_nopaste` and `remove_nopaste` aliases install and remove the hook, but use the setting to keep its displayed value consistent with the filter. These commands have no documented return value or success display beyond normal setting diagnostics.

## Example

```text
/load nopaste
/set nopaste on
```

Enable protection before ordinary channel use. To permit an intentional multiline paste, turn it off explicitly:

```text
/set nopaste off
```

Re-enabling it initializes a fresh counter and timestamp.

## Source references

- [script/nopaste](../../script/nopaste): setting, counter calculation, command exemption, and hook removal.
- [script/utime](../../script/utime): `utime_sub` subtracts seconds/microseconds timestamp pairs.
- [script/addset](../../script/addset): creates the Boolean setting and its callback.
- [source/functions.c](../../source/functions.c): `utime` returns the current timestamp; `strlen` counts code points.
- [source/hook.c](../../source/hook.c): a nonzero conditional-hook result suppresses default processing.
- [source/input.c](../../source/input.c): input submission clears the buffer and conditionally parses the submitted line; `input_clear_line` implements `erase_line`.
