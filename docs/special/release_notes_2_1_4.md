# EPIC5 2.1.4 release notes

Commit ID **1927**, release name **Redound**. The original heading and tagged source use **March 26, 2021**; the Git release commit `15341b23` is dated March 27 and says “I goofed the date - new release.” These dates describe their respective records rather than establishing a separate announcement date.

This page preserves historical EPIC5 changes and identifies current EPIC6 differences. See the [release index](release_notes.md).

## February 13, 2021: Unloading packaged timers

A timer created while loading a packaged script can be tagged with the load's package, allowing [`unload`](../commands/unload.md) to remove it along with that package's other definitions.

The announcement explicitly limited automatic tagging to load-time creation. A timer created later by a packaged alias does not automatically inherit the alias's package. A callback that creates a replacement timer likewise does not automatically transfer that package. In contrast, an existing repeating timer retains its own stored metadata and can be removed by package unloading.

Current source still assigns a new timer's package from the current load context, clones stored package metadata when updating timers, and removes timers whose package matches an unload request. Runtime ownership should not be inferred merely from the alias that issued the command. See [`timer`](../commands/timer.md).

## February 10, 2021: WINDOW UNCLEAR

The release added `/window unclear`, complementing `/unclear`, `/clear`, and `/window clear`:

```irc
/window unclear
```

The operation pulls retained scrollback into the selected window's view. It does not recover data already discarded from scrollback or lastlog. The operation remains registered in current EPIC6; see [window unclear](../window/unclear.md).

## February 7, 2021: Cut-buffer access through inputctl

The release added direct access to the input cut buffer:

```text
$inputctl(GET cutbuffer)
$inputctl(SET cutbuffer <new-text>)
```

GET returns the same saved text exposed by `$U`. SET replaces it without rewriting the visible input line. This avoids the old workaround of saving `$L`, erasing the input line, typing the desired text, cutting that text into the buffer, and restoring the original input.

There is one client-wide cut buffer. Later cut operations can replace it, so it is not persistent private storage for a script. In current source, SET returns the replacement text, and omitting that text clears it. See [`inputctl`](../functions/inputctl.md).

## February 5, 2021: SERVER_STATE terminology

The release introduced `SERVER_STATE` as the preferred term to reduce confusion with unrelated uses of “status.” It retained `SERVER_STATUS` for compatibility and recommended migrating new code to `SERVER_STATE`. The original note's assurance that old code would never break was a compatibility intention, not a guarantee for every future version.

Current EPIC6 registers both hook names and invokes SERVER_STATE followed by SERVER_STATUS with the same captured transition arguments: server reference, old state name, and new state name. They remain separate events rather than a single hook alias. The assignment occurs before the hooks, and their suppression results do not cancel it. See [server_state](../on/server_state.md) and [server_status](../on/server_status.md).

The corresponding `serverctl()` properties also retain both spellings:

```text
$serverctl(GET <refnum> STATE)
$serverctl(GET <refnum> STATUS)
```

Both currently return the server's state string. STATE is the preferred terminology. The release noted that bundled scripts had been updated; installing those scripts supplies their installed versions, but does not rewrite personal copies or startup files.

## July 3, 2020: WINDOW DELETE_KILL

The original motivation was that deleting a secondary screen leaves its windows hidden, while an ordinary window-kill operation cannot remove the final visible window on a screen. DELETE_KILL combines screen deletion with an attempt to remove the selected window:

```irc
/window delete_kill
```

The original release's caveats were that only the current/selected window was killed, with other windows left hidden, and that normal restrictions such as KILLABLE OFF could prevent deletion.

**Current EPIC6 distinction:** `windowcmd_delete_kill()` calls screen deletion and then directly attempts to unlink/delete the selected window. It does not check KILLABLE like ordinary `/window kill`. A refusal to close the main screen does not by itself prevent the subsequent window-deletion attempt; ordinary visible-window safety restrictions still matter. Therefore, the historical KILLABLE protection must not be assumed for this operation. See [window delete_kill](../window/delete_kill.md).

## Source validation

- Tag `epic5-2.1.4`, `source/irc.c` and `UPDATES`: release identity and dated announcements; Git commit `15341b23` records the date correction.
- [source/timer.c](../../source/timer.c), [source/commands.c](../../source/commands.c), and [source/alias.c](../../source/alias.c): load package context, timer metadata, and unload integration.
- [source/input.c](../../source/input.c) and [source/functions.c](../../source/functions.c): CUTBUFFER operations and `$U`.
- [source/hook.c](../../source/hook.c) and [source/server.c](../../source/server.c): both state hooks and STATUS/STATE property compatibility.
- [source/window.c](../../source/window.c): UNCLEAR registration and DELETE_KILL's direct deletion path.
