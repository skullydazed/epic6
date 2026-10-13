# $status_oneoff()

## Synopsis

`$status_oneoff(<window description> [status format])`

## Technical

Compiles and generates a temporary single status bar for the specified window, using the supplied format. Returns the generated text without installing that format in the window's persistent status configuration. Unlike [$status()](status.md), it generates a new result rather than reading an existing window cache.

The first argument is a normal function word identifying a window by name or numeric refnum; `0` selects the current input window. Quoted grouping for the description follows [/xdebug dword](../commands/xdebug.md). The complete remaining text is the format, including spaces and literal quotation marks. Omitting it supplies an empty format, not the window's existing format.

Formats use the normal percent-based status expandos and their associated settings. Generation uses the window's screen width, or the main screen's width for a hidden window. Text undergoes normal status normalization, truncation, padding, and right justification. [status_no_repeat](../settings/status_no_repeat.md) controls fill behavior. The returned string converts internal display attributes to IRC formatting controls and is not necessarily plain text.

If [status_does_expandos](../settings/status_does_expandos.md) is enabled, generation also expands EPIC variables and function calls in the generated text. This pass temporarily selects the target window and its server context, suppresses ordinary window display, then restores that context. Called functions can still have their own effects. Normal argument expansion can also occur before the built-in receives its format.

## Practical

Use this to preview a status format or generate status-style text for a particular window. There is no status-line selector: the temporary status object always has one bar, even if the real window uses double status bars.

Generating a changed temporary result triggers the usual `status_update` hook for the target window with screen-line index zero. It does not itself draw the returned status on the terminal or replace the real window's cached result, but hook handlers can perform their own actions.

## Returns

The generated status string. Missing window input or an invalid window description returns empty. An empty format normally yields status padding/fill according to the settings, rather than guaranteeing an empty return.

When status updates are currently suppressed, generation is deferred and the temporary cache remains unset. The current conversion helper then returns `<denormalize_string was called with NULL>`; it does not retry generation before discarding the temporary object.

## Restrictions

Format compilation uses fixed-size buffers and accepts at most 40 recognized status expandos; oversized formats can be truncated and later expandos omitted. This function has no options and does not require an IRC connection, though window-dependent expandos reflect the target's current state.

## Examples

`$status_oneoff(0 Nick: %N)` generates a single status for the current input window with its nickname expando. The result includes normal status formatting and fill.

`$status_oneoff(2 Left%>Right)` generates left/right-justified text for window 2, if that window exists.

Registration: [source/functions.c](../../source/functions.c). `function_status_oneoff()`, format compilation, generation, and hooks: [source/status.c](../../source/status.c). Window descriptions: [source/window.c](../../source/window.c). Display conversion: [source/screen.c](../../source/screen.c).
