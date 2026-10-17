# on signal

## Synopsis

```text
/on [<noise>]signal "<pattern>" [(<argument list>)] { <commands> }
```

## Description

Triggered when the main event loop processes signals recorded by EPIC's unified signal handler. Script handlers run later in ordinary event-loop processing, not directly inside the operating-system signal callback.

This does not cover every signal in every circumstance. Uncatchable signals such as `KILL` and `STOP` cannot generate it. Signals installed with operating-system default or ignored handling bypass the recording wrapper, and a signal that exits or crashes the client can prevent later scripted delivery.

Use standard [on](../commands/on.md) matching and noise controls. The pattern matches signal identifier and pending count together. There are no event-specific options.

## Parameters

| Parameter | Meaning |
| --- | --- |
| `$0` | Signal number on one invocation, then its symbolic name on another. |
| `$1` | Number of recorded occurrences of that signal still pending when the invocation is made. |

For each queued occurrence, EPIC invokes the hook twice: first using the number, then the symbolic name. It decrements the count afterward. A catch-all handler can therefore run twice per occurrence; prefer a symbolic-name pattern because numbers vary by platform.

If several occurrences are pending, successive pairs normally count down toward `1`. This is the client's recorded pending count, not a lifetime total or a guarantee of the number of signals sent: operating-system signal coalescing and arrival timing can affect it. Pending signals are processed in signal-number order rather than necessarily arrival order.

## Default behavior and limitations

There is no built-in display at scripted dispatch, and both hook return values are ignored. Suppression does not skip the symbolic-name invocation, cancel later occurrences, or reverse built-in signal handling.

The unified wrapper first records the signal and immediately calls any installed C callback. That callback can already have acted before scripts run. In particular, `USR1` prints a message and cleans up exec processes; the former log-reopening example does not prevent those effects. `HUP` and `TERM` normally enter client exit handling rather than waiting for a script to veto termination. Some modes change which signals are ignored.

Use this hook for notifications and synchronous script work after a caught signal, rather than as a general replacement for EPIC's signal behavior. Handling it does not itself send an IRC request.

## Example

Observe a caught resize signal once per symbolic invocation:

```text
on -signal "WINCH %" (name, pending) {
    echo Resize signal $name recorded (pending $pending);
};
```

The built-in screen-refresh handling remains in effect. Signal availability and delivery depend on the operating system and terminal environment.

## Source references

- [source/ircsig.c](../../source/ircsig.c): `my_signal` distinguishes default, ignored, and wrapped signals; `unified_signal_handler` records counts and invokes the C callback immediately.
- [source/irc.c](../../source/irc.c): `do_signals` emits numeric and symbolic events and decrements counts; startup installs signal-specific callbacks, including exec cleanup for `USR1` and exit handling for `HUP` and `TERM`.
- [source/hook.c](../../source/hook.c): event registration and `do_hook` implement matching.
