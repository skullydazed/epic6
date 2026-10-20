# paste script

## Synopsis

```text
/load paste
/set paste on|off|toggle
/set paste_strip on|off|toggle
/set paste_delay <seconds>
```

## Description

When `paste` is on, submitted input is sent as literal message text to the current target instead of being interpreted as client commands. This lets you paste code or text beginning with `/` without executing it. Turning paste mode off restores normal input handling. The default is `off`.

The script installs a Control-P binding to toggle paste mode. This binding is active immediately after loading; it is not merely a suggested optional binding. It replaces any previous Control-P assignment and is not restored when paste mode ends.

Because typed commands are also sent as text while paste mode is active, typing `/set paste off` into the input line does not turn it off. Press Control-P instead, or wait for automatic shutdown. Other key bindings can still execute commands normally.

## Settings

| Setting | Default | Meaning |
| --- | --- | --- |
| `paste` | `off` | Enable or disable literal submitted-input handling. Supports `toggle`. |
| `paste_strip` | `off` | Reconstruct each line from its first word and remaining words, stripping leading whitespace. Supports `toggle`. |
| `paste_delay` | `30` | Seconds after enabling paste mode before its automatic off command runs. |

These are script-provided settings created through [addset](addset.md), which is loaded automatically. The defaults are explicitly assigned on loading. The delay is a nonnegative integer; zero schedules immediate deferred shutdown rather than disabling the timeout. Changing it while paste mode is already active does not reschedule the existing timer.

With stripping off, the script passes the submitted line to the built-in `send` command as message text. With stripping on, it passes `$0 $1-`, which removes leading whitespace and reconstructs the separator after the first word; it is not an exact whitespace-preserving transformation.

## Effects and limitations

Enabling paste mode saves the Tab binding and binds Tab to [self_insert](../bind/self_insert.md), allowing a literal tab in the input buffer. It also saves and temporarily removes the entire [input](../on/input.md) hook list, then installs a suppressing wildcard hook that calls the built-in `send` command. Other input-hook processing, including history capture or paste protection implemented through those hooks, is suspended. Turning paste off restores the saved input hooks and Tab binding and defers deletion of its `PASTEOFF` timer.

The script prints `PASTE automatically turns off in <delay> seconds` when enabling it. This is a timer scheduled from enable time, not an inactivity timeout; sending lines does not extend it. Client event-loop delays and the normal [timer](../commands/timer.md) context rules can affect when it executes. If it expires partway through a paste, subsequent lines return to normal command interpretation. Do not rely on the timeout as a boundary around an arbitrarily long paste.

The target is selected anew for each submitted line: the current window's query recipient takes precedence over its channel. Switching windows or queries can redirect later lines. There is no paste buffer, preview, rate limit, server-specific protection, or delivery guarantee. Normal message encoding, outgoing hooks, connection requirements, and server limits still apply; literal input handling does not bypass them.

Paste mode is shared client state, not separate per window or connection. Other scripts changing the saved hook or binding stacks can interfere with restoration. Repeatedly assigning `paste on` while already on can push another set of saved state, so use Control-P to toggle rather than nesting enables. The internal `setup_paste` and `remove_paste` helpers are not safe nesting or independent-session interfaces. An active line-input prompt receives its callback input rather than the ordinary input hook, so paste mode does not replace that prompt's behavior.

There is a load-order caveat: the initial `set paste off` appears before the script defines `remove_paste`. On a first load without that alias already present, the callback attempts an undefined command rather than running the later cleanup helper. This does not install the paste-mode hook; the helper definitions are available after the load finishes. Reloading while active can instead call an existing helper and unwind saved state. Avoid loading or reloading this script in the middle of another script's temporary input mode.

No external program or optional platform feature is required. The script sends messages through the current connection; it is not an operating-system clipboard reader. Commands have no script-specific return value.

## Example

```text
/load paste
/set paste_delay 60
/set paste_strip on
```

Select the intended target, press Control-P, then paste the text and submit its lines. Press Control-P again before typing ordinary client commands. Set `paste_strip off` before enabling the mode when indentation should be retained.

## History

The paste script first appeared in EPIC4-1.1.13. The retained source is marked as 2003 work by hop and credits AnguzHawk's request for whitespace stripping.

## Source references

- [script/paste](../../script/paste): settings, Control-P binding, hook and Tab stacks, literal send, and automatic shutdown.
- [script/addset](../../script/addset): creates settings and callback bodies.
- [source/commands.c](../../source/commands.c): `send_to_query_first` sends text to the window target; command parsing distinguishes built-ins from aliases.
- [source/window.c](../../source/window.c): `get_window_target` selects query then channel.
- [source/hook.c](../../source/hook.c): `do_stack_on` saves, removes, and restores hook lists.
- [source/input.c](../../source/input.c): submitted-line handling and active input prompts.
- [source/keys.c](../../source/keys.c): key bindings and binding stacks.
- [source/timer.c](../../source/timer.c): scheduling and removal of the shutdown timer.
- [source/vars.c](../../source/vars.c): Boolean assignments invoke script callbacks even when assigning the same value.
