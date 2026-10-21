# Flush: removed command

The former standalone `/flush` command suppressed output from the current server for a redirect/wait cycle and discarded held output. EPIC6 has retired `/flush` along with `/redirect`; no bundled replacement provides that interface. Its historical timing and server-output suppression behavior are not available through a current built-in of that name.

Do not confuse it with these current window operations:

- [window flush](../window/flush.md), `/window flush`, discards queued display rows below the selected window's held page. It requires a held view and does not disable hold mode.
- [window flush_scrollback](../window/flush_scrollback.md) discards the window's display scrollback.
- [window clear](../window/clear.md) clears the current view without being the old server-output suppression command.

These operate on client window state. They do not tell a server to stop transmitting, erase saved logfiles, or recreate the former redirect/wait mechanism. See the individual operation pages for effects on display rows and retained lastlog records.

## Source references

- `source/commands.c`: standalone `flush` is absent from the command registry.
- `source/window.c`: distinct registered window operations and held-view flushing.
- `UPDATES` and `KNOWNBUGS`: retirement of `/redirect` and `/flush`.
