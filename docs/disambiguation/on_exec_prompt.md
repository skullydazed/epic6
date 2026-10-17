# on exec_prompt (automatic dispatch unavailable)

## Former synopsis

```text
/on [<noise>]exec_prompt "<pattern>" { <commands> }
```

## Description

The `exec_prompt` event formerly reported output from an [exec](../commands/exec.md) process that was not terminated by a newline. This let scripts react to interactive program prompts, rather than waiting for a complete output line.

EPIC6 retains the named hook registration, so `/on exec_prompt ...` is accepted. However, its current process reader does not generate the event: it uses line buffering and returns immediately for an incomplete line. The branch that selected `exec_prompt` for partial stdout or stderr output is compiled out.

A program that writes a prompt without a newline can therefore wait for input without triggering this hook. The former interactive `nslookup` example cannot be used as an automatically triggered prompt handler in the current implementation.

## Former parameters and display

The event supplied the process's logical name or numeric exec refnum in `$0` and the partial output text in `$1-`. The text could contain multiple words; it was not a single-word prompt argument.

Allowing default handling formerly displayed that partial output unless redirect metadata suppressed local display. There is no current automatic prompt display step to control through this hook. The `-linepart` and `-errorpart` exec options still store callbacks, but their partial-line reader branch is disabled too.

## Current alternatives and limitations

Complete stdout lines use [on exec](../on/exec.md), unless a nonempty `-line` callback replaces the hook. Complete stderr lines use [on exec_errors](../on/exec_errors.md), unless a nonempty `-error` callback replaces it. These are line-oriented interfaces and do not supply prompt notifications before a newline.

Because `exec_prompt` remains registered, a script can explicitly synthesize it with [shook](../commands/shook.md). That supplies only the arguments chosen by the script; it does not detect a process prompt or restore unbuffered process reading.

## Source references

- [source/exec.c](../../source/exec.c): `do_exec` passes the historical prompt event identifier to `handle_filedesc`; the active reader uses `dgets` with line buffering and returns for incomplete lines, while the partial-line hook and callback branch is disabled.
- [source/hook.c](../../source/hook.c): the hook table retains `exec_prompt`; `shookcmd` can explicitly invoke a registered event.
- [include/hook.h](../../include/hook.h): retains the event identifier despite the unavailable automatic dispatch.
