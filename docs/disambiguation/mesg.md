# Mesg: removed terminal-permission command

The former `/mesg [yes|no]` command inspected or changed permission for other users to write directly to the terminal. It was a local terminal-permission operation, unrelated to IRC private messages, and historically helped users avoid disruptive `talk` requests.

EPIC6 has retired this command and supplies no script replacement. `/mesg`, `/mesg yes`, and `/mesg no` therefore have no current built-in permission-setting or reporting contract. EPIC6 does not provide the former post-exit permission-persistence behavior through that interface.

If terminal write permissions matter on your system, use its operating-system facilities and documentation. Their exact permission model and session behavior are platform-specific; an IRC client command cannot override privileged operating-system access. For messages through IRC, see [msg](../commands/msg.md), which is a different operation.

## History and source

`UPDATES` records removal on August 30, 2026, explaining that the former talk-bomb protection was no longer relevant to the project.

- `source/commands.c`: current registry has no `mesg` handler.
- `UPDATES` and `KNOWNBUGS`: explicit retirement and its historical purpose.
