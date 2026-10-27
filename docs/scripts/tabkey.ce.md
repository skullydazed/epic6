# tabkey.ce script

The original page was empty. This reference describes the retained implementation in [script/tabkey.ce](../../script/tabkey.ce), including its current compatibility limits.

## Synopsis

```text
/load tabkey.ce
/set tabkey_complete_tolower off
/set tabkey_max_msg_history 6
/set tabkey_msghist_sent_only off
/set tabkey_nickcomp_suffix ,
```

The script reloads itself with the preformatted loader when necessary. It expects helpers from [builtins](builtins.md), including `encode`, `servernum`, and `winserv`, to be available; it does not load them itself.

## Completion and bindings

Loading creates three key actions and replaces these bindings without saving their previous assignments:

| Action | Effect | Default binding |
| --- | --- | --- |
| `TABKEY_COMPLETION` | Complete without cycling saved matches. | None. |
| `TABKEY_COMPLETION_FORWARD` | Complete and cycle forward through saved matches on repeated unchanged input. | Tab (`^I`). |
| `TABKEY_COMPLETION_BACKWARD` | Complete and cycle backward. | Escape followed by Tab (`^[^I`). |

Completion edits the input buffer; it does not submit the line. It can insert a unique result, extend a common prefix, display ambiguous candidates, or cycle saved candidates. State is shared in global `TABKEY.*` variables rather than isolated per window.

The script completes command/alias names and has context handlers for settings, variable names, joined channels, cached nicknames, and several command arguments. Ordinary text starts with nicknames from the current channel and falls back to other joined channels when needed; channel names can also contribute. A unique first-word nickname receives the configured suffix. On an empty line, or in the `/msg` target context, remembered message correspondents can supply targets.

Incoming `MSG` and outgoing `SEND_MSG` handlers maintain a per-server target history using hook serial **420**, moving repeated names to the recent end. With `TABKEY_MSGHIST_SENT_ONLY` on, incoming messages do not add entries. History is local script state, not a query of the server's message history.

## Settings

These are script-created setting objects, not built-in C settings. Loading reapplies the defaults.

| Setting | Default | Meaning |
| --- | --- | --- |
| `TABKEY_COMPLETE_TOLOWER` | `OFF` | Lowercase results in completion methods that explicitly call the normalization helper. It does not lowercase every possible nickname or result. |
| `TABKEY_MAX_MSG_HISTORY` | `6` | Intended message-target history limit. Use a positive integer; the handler removes one old entry only when the current count equals this value, so changing it does not reliably trim an already larger history. |
| `TABKEY_MSGHIST_SENT_ONLY` | `OFF` | Record outgoing correspondents only when on, instead of both incoming and outgoing correspondents. |
| `TABKEY_NICKCOMP_SUFFIX` | `,` | Suffix for a unique first-word nickname completion. |

## Current compatibility limits

- File-completion contexts call `tabkey.method.file`, but the bundled script tree contains no definition of that alias. Their presence does not establish working file completion; another loaded script would have to provide the helper.
- The notify fallback calls `$notify(on)`. The supplied [notify function](../disambiguation/notify_function.md) currently returns empty, so it contributes no online-notify candidates.
- Retained DCC commands and DCC hooks refer to removed EPIC6 facilities. The hardcoded WINDOW completion list also contains historical operations; a suggested name is not proof that the current command supports it.
- Nickname completion uses cached membership, not a fresh network lookup. Historical command handlers and custom script definitions can change which candidates are useful.

The script defines many `tabkey.*` aliases, creates settings and key actions, and installs hooks without a package declaration or automatic restoration command. Loading another completion script can replace its bindings or definitions. Inspect the edited input before sending it.

## History and source validation

The script header identifies version **1.3.1**, based on CrazyEddy's EPIC4 script and extended by Brian Weiss. The empty original page supplied no inclusion date.

- [script/tabkey.ce](../../script/tabkey.ce): bindings, setting defaults, completion dispatch, history, and retained compatibility branches.
- [source/functions.c](../../source/functions.c): BINDCTL, SYMBOLCTL, GETCOMMANDS, GETSETS, CHANUSERS, and MYCHANNELS registrations.
- [source/hook.c](../../source/hook.c): current MSG/SEND_MSG/SEND_CTCP hooks and absence of the former DCC hook registrations.
- [script/builtins](../../script/builtins) and [script/notify](../../script/notify): helper definitions and unfinished notify-function branch.
