# autoget

## Status in EPIC6

The `autoget` script formerly automated incoming DCC file transfers. Its file remains in the repository, but EPIC6 removes the `/dcc` transfer command, the `dcc_request` hook, and the DCC settings on which it depends. Loading the script does not restore automatic downloads, resuming, or rejection of transfers. There is no direct built-in replacement for this transfer facility.

## Former interface

```text
/load autoget
/autoget [<pattern> | -<pattern> ...]
/autoreget [<pattern> | -<pattern> ...]
/autoreject [<pattern> | -<pattern> ...]
/autoclobber [get|resume|rename|close] [get|resume|rename|close]
```

These are script-defined aliases, not C built-in commands. The pattern aliases display their lists without arguments, append patterns supplied as arguments, and remove an exact listed pattern when prefixed with `-`; removal does not use wildcard matching.

The former transfer handler matched patterns against `nick!user@host,IP,file`, replacing commas in the filename with dots for matching. Its initial new-file and existing-file pattern lists are both `*`. Such masks are not a reliable authentication mechanism.

`autoget` was intended to accept files not already present through [dcc get](ctcp_get.md). `autoreget` was intended to handle existing files through [dcc resume](ctcp_resume.md) or [dcc close](ctcp_close.md). `autoclobber` selects separate actions for a smaller local file and a local file at least as large as the offer, defaulting to `resume close`. Its retained alias accepts `get`, `resume`, `rename`, and `close`; without arguments it prints usage and both modes, and an unrecognized mode produces an error. `rename` was intended to append `~` and a numeric suffix to the local filename before requesting a fresh download. These transfer effects are unavailable in EPIC6.

## Retained script limitations

The script also contains inconsistencies independent of the removed transfer support. The pattern aliases edit variables such as `autoget.getpatterns`, whereas the handler reads variables such as `autoget.patterns.get`. Consequently, changing the alias lists does not change the handler's lists. The handler's rejection branch merely skips its own download action; it does not issue the close operation formerly attributed to `autoreject`.

The script still references removed settings including [dcc_store_path](set_dcc_store_path.md), [mirc_broken_dcc_resume](set_mirc_broken_dcc_resume.md), and [no_ctcp_flood](set_no_ctcp_flood.md). Its old warnings about enabling resume or disabling CTCP flood protection do not provide a working EPIC6 configuration. Remove obsolete automatic-transfer setup from startup scripts.

## History

The autoget script first appeared in EPIC4-1.1.1 and was substantially enhanced in EPIC4-1.1.8.

## Source references

- [script/autoget](../../script/autoget): retained aliases, defaults, pattern-variable mismatch, and former transfer handler.
- [source/commands.c](../../source/commands.c), [source/hook.c](../../source/hook.c), and [source/vars.c](../../source/vars.c): current command, hook, and setting registrations omit the required DCC interfaces.
- [source/ctcp.c](../../source/ctcp.c): no built-in DCC transfer negotiation handler.
- [KNOWNBUGS](../../KNOWNBUGS): records retirement of `/dcc`, `$dccctl()`, and associated functionality.
