# window search_forward

## Synopsis

```text
/window [<window>] search_forward ["<regex>"]
```

## Description

Search forward toward newer output in the selected window's scrollback, moving the active browsing view to a matching display row. With no window selector, use the current window. The starting row is skipped so repeating the operation seeks the next newer match.

The pattern is a case-insensitive POSIX extended regular expression. Matching uses plain text derived from each retained display row, with formatting removed. Wrapped rows are tested separately, so a pattern cannot span a wrap boundary. Matching text is not highlighted.

With no pattern, reuse the last successfully compiled pattern supplied to this operation or [window search_back](search_back.md). The saved pattern is global, shared across windows. A newly supplied invalid pattern reports `The regex [<pattern>] isn't acceptable because [<error>]` and discards the old pattern. Without a saved pattern, EPIC reports `Need to know what to search for`.

## Views and failures

If a scrollback view is active, search starts from its top and advances that view. Once it catches up to the normal or held view, EPIC automatically cancels scrollback. If no scrollback view is active but the held view is older than the normal scrolling view, the search instead advances the held view. It does not turn [window hold_mode](hold_mode.md) off or create a separate scrollback view in that branch.

If neither view can advance, or no matching row is found before the blank end-of-output marker, EPIC requests a bell and leaves the view unchanged. The marker itself is not tested. Bell output is subject to [set beep](../set/beep.md) and terminal support. A successful search requests body and status updates and normally produces no confirmation message.

Only retained scrollback is searched, not logfiles or deleted history. An invisible window can be searched without being shown. Input selection is unchanged, and no IRC request is sent. Use [window scroll_end](scroll_end.md) to advance toward the end when finished, and see [window](../commands/window.md) for view details.

## Arguments and examples

The pattern is one argument; use double quotes for spaces. Additional words are processed as subsequent window operations. A word immediately after `search_forward` is treated as a new pattern, so a no-pattern repeat is normally placed at the end of a `/window` command. `/window -search_forward` is a no-op. Compilation and no-match failures do not inherently stop subsequent operations.

```text
/window scroll_start
/window search_forward "frob$"
/window search_forward
/window scroll_end
```

Move toward the oldest output, find a newer row ending in `frob`, repeat the search, and advance toward the end. The search skips the starting row, including the oldest row if that is where `scroll_start` placed the view.

## History

This operation first appeared in EPIC4-1.1.8.

## Source references

- [source/window.c](../../source/window.c): `windowcmd_search_forward`, `new_search_term`, `last_regex`, `window_scrollforward_to_string`, `window_scroll_regex_tester`, and `window_scrollback_forwards` implement parsing, shared patterns, plain-row matching, and view selection. `recalculate_window_cursor_and_display_ip` cancels caught-up scrollback.
- [source/term.c](../../source/term.c): `term_beep` applies beep settings to failed searches.
