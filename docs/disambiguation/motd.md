# Motd

Do you mean:

- The [motd command](../commands/motd.md), `/motd [server-or-nickname]`, which requests an IRC server's message of the day? The server controls supported target forms, content, and replies.
- The [motd script](../scripts/motd_script.md), loaded with `/load motd`, which displays a local installation's `ircII.motd` file when newer than the user's `~/.ircII.motd` timestamp marker?

These are independent. The built-in command sends a server request; loading the script reads local file timestamps, uses the `less` pager, and starts an external `touch` command to update the marker after attempting display. It does not request the IRC server's MOTD or replace the built-in command.

See the individual pages for reply handling, local file locations, dependencies, and limitations.

## Source references

- `source/commands.c`: `motd` registration and generic protocol sender.
- `source/numbers.c`: server numeric handling.
- `script/motd`: local timestamp comparison, pager, and marker update.
