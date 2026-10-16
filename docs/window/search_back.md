# window search_back

## Synopsis

```text
/window [<window>] search_back ["<regex>"]
```

## Description

Search backward toward older output in the selected window's scrollback, placing a matching display row at the top of the scrollback view. With no window selector, use the current window. The search skips the starting row so repeating it looks for an older match.

`<regex>` is a case-insensitive POSIX extended regular expression. Matching uses plain text derived from each retained display row, with display formatting removed. It searches wrapped rows separately rather than reconstructing an original multirow message, so a pattern cannot span a wrap boundary. A match is not highlighted.

With no pattern, reuse the last successfully compiled pattern supplied to either this operation or [window search_forward](search_forward.md). That pattern is shared globally across windows. Supplying an invalid pattern discards the previous one and reports `The regex [<pattern>] isn't acceptable because [<error>]`. A later repeat then reports `Need to know what to search for` until another pattern is supplied.

## Views and limits

If already in scrollback, search starts from its top row. Otherwise it starts from the held view when that view is older than the normal view, or from the normal view. A successful search selects the resulting scrollback view and requests body and status updates without changing hold mode. Use [window scroll_end](scroll_end.md) to move toward the normal view afterward; see [window](../commands/window.md) for view details.

If no match is found before reaching the oldest retained row, EPIC requests a bell and leaves the view unchanged. The current helper checks the oldest-row boundary before testing that row, so a match occurring only on the oldest row is not found by this operation. A search already starting at that boundary also requests a bell. Bell output is subject to [set beep](../set/beep.md) and terminal support.

Only retained scrollback is searched, not external logfiles or deleted history. An invisible window can be searched without being shown. Input selection is unchanged, and no IRC request is sent.

## Arguments and examples

The pattern is one argument; use double quotes for spaces. Following words are processed as subsequent window operations. A word immediately after `search_back` is always treated as a new pattern, so a repeat search without a pattern is normally placed at the end of a `/window` command. `/window -search_back` is a no-op. Compilation or no-match failures do not inherently stop subsequent operations.

```text
/window search_back "boo.*ya!"
/window search_back
/window 3 search_back "connection lost"
/window scroll_end
```

Search for an older match, repeat the shared pattern, search window 3 for a phrase, or advance toward the end when finished.

## History

This operation first appeared in EPIC4-1.1.8.

## Source references

- [source/window.c](../../source/window.c): `windowcmd_search_back`, `new_search_term`, `last_regex`, `window_scrollback_to_string`, `window_scroll_regex_tester`, and `window_scrollback_backwards` implement parsing, shared pattern lifetime, plain-row matching, view selection, and boundary behavior.
- [source/term.c](../../source/term.c): `term_beep` applies beep settings to unsuccessful searches.
