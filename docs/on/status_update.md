# on status_update

## Synopsis

```text
/on [<noise>]status_update "<pattern>" [(<argument list>)] { <commands> }
```

## Description

Triggered when EPIC generates a changed status bar for a window. It works in both full-screen and dumb modes, and for hidden as well as visible windows. The event reports status generation, before any physical screen redraw.

Use standard [on](../commands/on.md) matching and noise controls. The pattern matches the window refnum, status line number, and generated text together. There are no event-specific options.

## Parameters

| Parameter | Meaning |
| --- | --- |
| `$0` | User-visible refnum of the window whose status was generated. |
| `$1` | Status line position: `0` for the first line, `1` for the second. |
| `$2-` | Generated status bar, including formatting controls and padding. |

With one status line, position `0` uses the single-line status format. With two lines, positions `0` and `1` use the first and second double-line formats respectively; these are positions, not status-format slot numbers.

## Effects and limitations

EPIC stores the newly generated status text before invoking the hook. The hook's suppression result is ignored: it cannot cancel the update or replace the status text by changing its arguments. There is no separate built-in message from this event.

A normal recalculation that produces exactly the cached status string does not trigger it. A forced redraw of unchanged text also does not itself trigger it. Initial generation, or generation after a cached result was cleared, can trigger the event even without a change in the underlying window state. Deferred status updates do not trigger it until generation is allowed to run.

The text has already undergone status formatting, optional `status_does_expandos` expansion, display normalization, width limiting, padding, and a final attribute reset. It is not the raw format string or necessarily plain printable text. Hidden windows use the main screen's width when generating their status.

[Status_oneoff](../functions/status_oneoff.md) also uses this generation path with a temporary status object and can produce this event for its generated text. An event therefore does not guarantee that a visible status bar was drawn. Avoid calling that function unconditionally from this handler, since it can trigger another `status_update`.

## Example

Observe which status line changed without printing its formatting controls:

```text
on -status_update "*" (win, line, text) {
    echo Status generated for window $win, line $line;
};
```

## Source references

- [source/status.c](../../source/status.c): `make_status` generates and caches status text before invoking the hook; `redraw_status` separately draws it; `function_status_oneoff` uses a temporary status object.
- [source/window.c](../../source/window.c): `update_all_windows` separates status recalculation from forced redraws.
- [source/hook.c](../../source/hook.c): event registration and standard hook matching.
