# Environment settings relevant to EPIC

Environment variables are strings inherited from the process that starts EPIC, usually your shell. Their names are case-sensitive. Use the exact uppercase names below; environment lookup differs from EPIC's case-insensitive script-variable names.

For a POSIX-style shell such as sh or bash:

```sh
export IRCNICK="example"
export IRCSERVER="irc.example.org"
epic6
```

For csh or tcsh, the equivalent assignment is `setenv IRCNICK "example"`. These are shell commands, not EPIC commands. Environment assignments made after a client has already started do not update that running process.

## Startup identity and connections

| Variable | Meaning |
| --- | --- |
| `IRCUSER` | Initial `default_username`; falls back to the local account name, then `default` when needed. `-z` overrides it. |
| `IRCNICK` | Initial nickname. If absent or empty, try `NICK`, then the default username. `-n` or a positional nickname can override it. |
| `IRCNAME` | Initial realname. If absent or empty, try `NAME`, `REALNAME`, the existing `default_realname`, account description when available, then the default username. |
| `IRCHOST` | Default local virtual-host choice on a multihomed system. `-H` overrides it; the selected host/address must be usable locally. |
| `IRCSERVER` | Whitespace-separated [server descriptions](server_description.md) inserted into the server list after command-line server entries. |
| `IRCPORT` | Default port when a description does not select its own. Starts from `6667` without this override; `-p` overrides it. |
| `IRCUMODE` | Requested user modes used during registration/reinstatement when appropriate. The server determines accepted modes. |
| `IRCCHANNEL` | One-shot channel text requested after the first successful registration, overridden by `-c`. It is not a persistent autojoin list. |
| `IRC_SERVERS_FILE` | Filename used by the default-server-file importer. It is read when the assembled server list is empty or `-a` requests the import. Without this variable, that importer returns without loading a default file. |

A startup choice is not confirmation that an identity, mode, join, port, or server connection will be accepted. Server descriptions and later runtime commands can choose different values. See [command-line options](command_line.md) for argument precedence and the positional nickname heuristic.

## Startup and library paths

| Variable | Meaning |
| --- | --- |
| `EPICRC` | Preferred alternate startup-file path when present. Takes precedence over `IRCRC`. |
| `IRCRC` | Alternate startup-file path when `EPICRC` is absent. |
| `IRCLIB` | Overrides the compiled client library directory and is used to initialize the default load path. |
| `IRCPATH` | Initial [load_path](../set/load_path.md) setting. If absent, the compiled template expands to `~/.epic:~/.irc:<library>/script:.`. |

Without `EPICRC` or `IRCRC`, startup chooses `~/.epicrc` if it exists, otherwise `~/.ircrc`. `-l` and `-L` replace the selected path. If the selected file is not readable at load time, the client loads the supplied [global](../scripts/global.md) script instead. `-q` skips this startup load, including the fallback. A personal startup file replaces that fallback path rather than always running after it.

Path values are initialized at startup; changing `IRCPATH` afterward does not automatically change `load_path`, and changing `EPICRC` does not reload configuration. Use the corresponding runtime command or setting. Empty and unset environment values are not interchangeable for every name: some branches test presence, while others explicitly reject empty text.

## Terminal, local programs, and scripts

`TERM` selects the terminal capability description. If it is absent or terminal setup fails, the client falls back from fullscreen operation. It also affects terminal-derived [key bindings](default_keybindings.md).

The home-directory choice uses a retrieved account entry when available, then `HOME`, then `/` as a last fallback. Do not assume changing `HOME` necessarily changes every path EPIC initialized. The locale environment also influences the initial system locale, while the client explicitly keeps numeric locale handling in the C locale and has its own UTF-8/encoding setup.

`PATH` is relevant to shell commands and executable/helper discovery. Scripts can read further names: for example, the optional [mail](../scripts/mail.md) implementation uses `MAIL`, `MAILDIR`, and `LOGNAME` for local mailbox discovery. Those are script-specific dependencies, not a statement that the core monitors mail whenever the variables exist. Linked libraries can consult additional environment variables of their own.

## Access from scripts

Environment strings are a fallback for normal expando lookup when a visible local, global assignment, built-in expando, or registered setting has not supplied the value. For example:

```text
/echo Home environment: $HOME
```

A script definition with the same name can shadow that lookup. An ordinary `assign` changes a script variable, not the process environment. To change the running client's environment and that inherited by later child processes, use [setenv](../commands/setenv.md):

```text
/setenv EXAMPLE_VALUE some text
/setenv -EXAMPLE_VALUE
```

The second command unsets that name. Neither command alters the parent shell's environment, and neither causes startup initialization to be repeated. Environment access returns strings; individual commands and settings apply their own parsing, validation, and errors. No IRC request is sent merely by reading or changing an environment value.

## Source references

- [source/irc.c](../../source/irc.c): startup environment selection, fallbacks, command-line overrides, locale setup, and startup loading.
- [source/server.c](../../source/server.c): server-file import, registration, and one-shot channel/mode handling.
- [source/term.c](../../source/term.c): terminal environment and setup fallback.
- [source/alias.c](../../source/alias.c): environment fallback after client-variable lookup.
- [source/commands.c](../../source/commands.c): `setenvcmd` changes the process environment.
- [include/config.h](../../include/config.h): default load-path template.
- [script/mail.py](../../script/mail.py): script-specific mailbox environment discovery.
