# Command line

## Synopsis

```text
epic6 [options] [nickname] [server-description ...]
```

The executable accepts short options through the system `getopt` interface. Put options before positional arguments for portable behavior; use `--` to end option parsing when necessary. Shell quoting applies before EPIC receives arguments, so quote paths or server descriptions containing shell-significant characters.

## Options

| Option | Implemented effect |
| --- | --- |
| `-a` | Attempt to import the server file named by `IRC_SERVERS_FILE` even when server descriptions were supplied through arguments or `IRCSERVER`. |
| `-b` | Run detached bot mode; also disables the fullscreen interface. |
| `-c channel` | Set the channel text to join after the first successful server registration, overriding `IRCCHANNEL`. |
| `-d` | Disable the fullscreen interface and use dumb input/output mode. |
| `-h` | Print usage to standard error and exit with status `1`. |
| `-H host` | Set the default local virtual-host choice, overriding `IRCHOST`; it must be usable on the local system. |
| `-l file` or `-L file` | Select the startup file instead of the environment/default choice. They are equivalent. |
| `-n nickname` | Set the default nickname, overriding `IRCNICK`; a positional nickname can override this again. |
| `-p port` | Set the default server port, overriding `IRCPORT`. An explicit port in a server description can select its own port. |
| `-q` | Skip startup-file loading, including the fallback `global` script. |
| `-s` | Skip the automatic initial connection; choose a connection later through `server` or startup code. |
| `-v` | Print version information and exit successfully. |
| `-x` | Enable a broad internal `xdebug` bit mask. This changes debugging/compatibility behavior, not just the displayed verbosity. |
| `-z username` | Set `default_username`, overriding `IRCUSER`. The server still controls the accepted IRC identity. |
| `-B` or `-S` | Accepted historical compatibility options with no current effect. |

Options are case-sensitive. The former `-f`, `-F`, `-o`, and `-O` switches are not accepted by this parser. Unknown options or missing required option arguments lead to usage output and exit status `1`. The usage text still describes behavior for `-B` and `-S` that their current switch branches do not perform.

## Nickname and server selection

After options, the first nonempty positional argument is treated as a nickname if it contains **no period**. This is a simple text heuristic, not full nickname or server-description validation. A short server hostname or alias without a period can therefore be consumed as the nickname even when `-n` was supplied. All remaining nonempty arguments are interpreted as [server descriptions](server_description.md).

Before option overrides, the nickname comes from `IRCNICK`, then `NICK`, then the default username. The default nickname buffer holds up to 30 bytes. Longer choices are truncated in startup processing, and the server can impose a shorter limit or reject the name. If the selected nickname is empty, the default username supplies the fallback.

Command-line server descriptions are inserted first. Whitespace-separated descriptions from `IRCSERVER` are then inserted too; they are not excluded merely because command-line servers exist. If the resulting list is empty, or `-a` was used, EPIC attempts its default-file import. In this implementation that import requires `IRC_SERVERS_FILE`; with the variable absent, it returns without importing a file. Without `-s`, normal startup selects server entry zero for the initial connection.

The compiled initial default port is `6667`, subject to `IRCPORT`, `-p`, and server-description settings. Port input uses the client's numeric conversion rather than strict range validation at the option parser. Transport choice, address resolution, TLS, server passwords, and registration follow the server-description and connection code; selecting an option is not confirmation that a connection can succeed.

## Startup files

The initial startup-file choice is `EPICRC` if present, otherwise `IRCRC`. Without either variable, EPIC chooses `~/.epicrc` if it exists, otherwise `~/.ircrc`. `-l` or `-L` replaces this choice. At load time, an unreadable selected file falls back to the supplied [global](../scripts/global.md) script rather than necessarily causing startup to abort.

Startup configuration is loaded before the automatic initial connection. A personal startup file replaces that default startup path; `global` is not always run first. `-q` prevents this entire load step, even when an explicit `-l` file was selected. `-s` prevents the automatic connection but does not prevent a startup script from requesting connections itself.

The `-c` channel text is used once, after successful registration, then cleared. It does not create a persistent autojoin list. The server decides whether the join is permitted.

## Terminal and bot operation

Fullscreen mode needs a suitable terminal. When standard input is not a terminal, normal startup also falls back to dumb mode. `-b` forks and detaches, redirects standard input/output/error to `/dev/null`, and ignores hangup signals in the detached client. Arrange automation and logging through scripts; there is no interactive terminal left for ordinary input. Availability of external services and connection policies still applies.

These are executable startup options, not commands entered at the client prompt. They have no script-function return value. Version/help exit immediately; a normal launch enters the client's event loop and reports connection or configuration problems through its usual output paths.

## Examples

```sh
epic6 -s -n example
# Start without loading startup scripts or connecting automatically:
epic6 -q -s -n example
# Use a particular startup file and a dotted server name:
epic6 -l "$HOME/.config/epic/startup.irc" example irc.example.org
```

Use the first form to inspect the client before selecting a server. With `-q`, support aliases normally supplied by startup scripts will not be present until explicitly loaded.

## Source references

- [source/irc.c](../../source/irc.c): option string and switch branches, environment precedence, nickname heuristic, startup selection, bot mode, and initial connection.
- [include/irc.h](../../include/irc.h): nickname buffer limit.
- [source/server.c](../../source/server.c): registration consumes the one-shot default channel and implements server connection behavior.
- [source/server.c](../../source/server.c): server-description insertion and `IRC_SERVERS_FILE` import.
