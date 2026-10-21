# Botmode: removed command

The former `/botmode` command converted an already running dumb-mode client into a detached bot, allowing a script to interact with the user before detaching. EPIC6 no longer supports that command or switching between attached and detached operation after startup.

Use the `-b` [command-line option](../special/command_line.md) when starting the client instead:

```sh
epic6 -b -l /path/to/bot.irc mynick irc.example.net
```

Replace the script path, nickname, and server with your own values. `-b` selects detached, non-fullscreen operation at startup. The client forks; the parent reports the child PID and exits, while the child starts a new session and redirects standard input, output, and error to `/dev/null`. Startup can fail if process creation or session setup fails.

A detached script cannot rely on interactive prompts through those standard streams. Prepare its configuration before starting and arrange any required logging in the script. Bot mode does not itself implement automation, reconnection, or server authentication; those remain script and network concerns. The `-d` option selects non-fullscreen operation but does not provide a later `/botmode` transition.

## History and source

The command appeared in EPIC5-0.0.2. `UPDATES` records its removal on August 30, 2026.

- `source/irc.c`: `-b` parsing, fork/session setup, stream redirection, and startup mode.
- `source/commands.c`: current command registry has no `botmode` entry.
- `UPDATES` and `KNOWNBUGS`: removal and the startup-option replacement.
