# EPIC5 2.1.2 release notes

Released **May 11, 2020**, commit ID **1908**, release name **Lugubrious**. Tag `epic5-2.1.2` and release commit `f40fa75c` confirm that identity.

These are historical EPIC5 announcements. The source checks below correct mistakes in the original API descriptions and identify current EPIC6 differences. See the [release index](release_notes.md) and the current [`exec`](../commands/exec.md) and [`execctl`](../functions/execctl.md) references before using the interfaces.

## May 9, 2020: execctl process control

`execctl()` introduced low-level inspection and control of EXEC process descriptions. Some fields accept values without verifying that referenced objects exist. The release recommended the higher-level EXEC command when its validation is needed.

| Operation | Meaning |
| --- | --- |
| `REFNUMS` | List existing process descriptions, such as `%1 %2`; the original incorrectly called these bare integers. |
| `REFNUM <description>` | Resolve a process description such as `%3` or `%myproc` to its integer reference. |
| `NEW <commands>` | Create a process description without launching it. |
| `LAUNCH <refnum>` | Launch a description that has not started. |
| `CLOSEIN <refnum>` | Close the child's input, allowing an EOF-driven program to finish reading. |
| `CLOSEOUT <refnum>` | Close stdout/stderr pipes and stop receiving output. This does not itself guarantee child termination; subsequent writes can fail. |
| `SIGNAL <refnum> <signal>` | Signal the process using a number or short signal name. The original reversed these arguments and contained a malformed example. |
| `GET <refnum> <field>` | Read a property. |
| `SET <refnum> <field> <value>` | Change a writable property. |

For example, `$execctl(SIGNAL 3 HUP)` selects process reference 3 and signal HUP. Integer references for these operations do not carry the `%` prefix used in process descriptions.

### Process fields

All fields below can be read. “Writable” identifies the setters available in current source; command text and direct-execution mode cannot be changed after launch.

| Field | Writable | Meaning / current qualification |
| --- | --- | --- |
| `REFNUM` | No | Integer process reference, allocated monotonically within the client session. |
| `REFNUM_DESC` | No | Numeric target description, such as `%3`. |
| `LOGICAL` | Yes | Unique user-supplied process name. |
| `LOGICAL_DESC` | No | Named target description, such as `%myproc`; follows changes to LOGICAL. |
| `COMMANDS` | Before launch | Stored command text. |
| `DIRECT` | Before launch | `1` selects direct execution; `0` uses the configured shell where available. |
| `REDIRECT` | Yes | Intended protocol, `PRIVMSG` or `NOTICE`; current output forwarding is incomplete, as explained below. |
| `WHO` | Yes | Intended output destination; stored but unused by the current output reader. |
| `WINDOW_REFNUM` | Yes | Output window reference; setting a positive number does not validate window existence. |
| `SERVER_REFNUM` | Yes | Server context; setting a nonnegative number does not validate server existence, and launch overwrites it with the current context. |
| `LINES_LIMIT` | Yes | Combined received-line limit. Zero falls back to `shell_limit`, correcting the original “no limit” claim. |
| `STDOUTC` | Yes | Commands for complete stdout lines. |
| `STDOUTPC` | Yes | Stored commands for partial stdout; current line-buffered reading does not invoke this path. |
| `STDERRC` | Yes | Commands for complete stderr lines. |
| `STDERRPC` | Yes | Stored commands for partial stderr; subject to the same current limitation. |
| `EXITPC` | Yes | Cleanup callback text; supported by the source although omitted from the original field table. |
| `PID` | No | Operating-system process ID; `-1` before launch. |
| `STARTED_AT` | No | Creation or most recent launch time, currently returned as seconds and nanoseconds. |
| `P_STDIN` | No | Operating-system descriptor for the child's input. |
| `P_STDOUT` | No | Operating-system descriptor for stdout. |
| `P_STDERR` | No | Operating-system descriptor for stderr. These descriptors are not EPIC `$read()`/`$close()` handles. |
| `LINES_RECVD` | No | Received-line count across both output streams. |
| `LINES_SENT` | No | Stored counter; the current sending implementation does not increment it. |
| `EXITED` | No | Whether the process has exited. Zero is the initial not-exited state. |
| `TERMSIG` | No | Termination signal when recorded. Initially zero; the original's universal `-1` sentinel description is inaccurate for current source. |
| `RETCODE` | No | Exit code when recorded. Initially zero, which must not be read as proof of successful completion. |
| `DUMB` | No | Output-ignored/closed state; not merely an unused reserved slot. |
| `DISOWNED` | No | Internal process state, including administrative termination of an unlaunched description. |

Descriptions are removed during process cleanup, so fields are not a permanent exit-history database. The current low-level reference documents return values and rejection cases in detail.

## May 8, 2020: EXEC -NOLAUNCH

`-NOLAUNCH` creates or modifies a description without starting an unlaunched process. A later EXEC reference can launch it:

```irc
/exec -nolaunch -name myproc ls -l
/exec -nolaunch -limit 5 %myproc
/exec %myproc
```

Operations requiring a live child or open pipes cannot perform that work before launch.

## May 7, 2020: uuid4

`$uuid4()` was introduced to generate a random version-4 UUID string. The original release reserved all arguments for future use and recommended passing none. Current EPIC6 also recognizes `nodashes`; see [`uuid4`](../functions/uuid4.md) for its current behavior and random-generator error limitation.

## May 7, 2020: SEND_EXEC hook

The [`send_exec`](../on/send_exec.md) event allows scripts to replace the local echo of text sent to an EXEC process, including through EXEC input, `/msg %process`, or a query target. Current arguments are the logical name or process description, numeric process reference, and text. Suppression controls the echo after the write attempt; it is not a way to cancel the write or prove delivery.

## May 7, 2020: Revised EXEC interface

The release reworked process creation, querying, output routing, and flag combinations. An invocation creates or modifies one process description:

```text
/exec [options] %refnum [extra-arguments]
/exec [options] %logical-name [extra-arguments]
/exec [options] commands-to-run
/exec [options] (commands-to-run) [extra-arguments]
```

### Stream operations

| Option | Purpose |
| --- | --- |
| `-CLOSE` | Close the process's input/output pipes and ignore further output; not an unconditional kill command. |
| `-CLOSEIN` | Close stdin. |
| `-CLOSEOUT` | Close stdout and stderr. |
| `-IN <text>` | Send text to the selected process. |

### Naming and routing

| Option | Historical purpose / current distinction |
| --- | --- |
| `-NAME <name>` | Set a logical process name. |
| `-OUT` | Select the current channel as intended output destination. |
| `-WINDOW` | Select the current output window. |
| `-WINTARGET <window>` | Select an output window by its descriptor. |
| `-MSG <target>` | Select intended PRIVMSG output redirection. |
| `-NOTICE <target>` | Select intended NOTICE output redirection. |

The original allowed any message target, including DCC Chat, for output redirection. EPIC6 has removed DCC. More generally, the current EXEC reader stores redirection settings and suppresses ordinary output when set, but does not actually forward those lines to IRC; do not rely on the old “send all output” promise in this implementation.

### Script callbacks

| Option | Callback input |
| --- | --- |
| `-LINE {code}` | Each complete stdout line as `$*`. |
| `-LINEPART {code}` | Intended partial stdout callback; not invoked by the current line-buffered reader. |
| `-ERROR {code}` | Each complete stderr line as `$*`, correcting the original's “incomplete line” typo. |
| `-ERRORPART {code}` | Intended partial stderr callback; subject to the same limitation. |
| `-END {code}` | Cleanup callback: `$0` logical name or numeric reference, `$1` termination signal, `$2` exit code. |

Cleanup follows both child exit and output processing, so it can lag the operating-system exit. A nonempty explicit callback replaces the corresponding normal hook/display behavior.

### Other options and targets

`-DIRECT` bypasses the shell; shell expansion features such as globbing then do not occur automatically. `-LIMIT <number>` overrides the process's received-line limit. A positive reached limit closes its pipes and ignores further output; zero uses the global fallback. `-<signal-number>` and `-<signal-name>` request signaling of the selected process.

`%<reference>` and `%<logical-name>` are message targets for process input and may be selected with `/msg` or `/query`. Their behavior remains local process I/O, separate from an IRC channel or nickname.

## April 26, 2020: Python shortener script

The release introduced an in-client URL shortening demonstration:

```irc
pyload shortener
```

The retained script detects HTTP/HTTPS words in selected message hooks and prints loopback URLs such as `http://127.0.0.1:8080/1`, which redirect to the stored original URL. The numeric identifier depends on previously collected entries; it is not always 1. The original illustration used a long URL that wrapped across the display, followed by the shorter local URL.

Current `script/shortener.py` starts a server bound to `127.0.0.1:8080` when imported and requires optional Python support plus the `epic` module and `pyload` alias. A browser on another machine cannot reach the EPIC host's loopback address merely by using the same URL. These are historical feature notes, not evidence that the service has been started or tested during this migration.

## February 24, 2019: Previous release

The source notes recorded EPIC5 2.1.1, Abulia, as the preceding release, acknowledging that some planned work remained unfinished. See its [release notes](release_notes_2_1_1.md).

## November 28, 2018: EXEC -WINTARGET

The release notes also carried the earlier announcement that `-WINTARGET` selects a window by name or descriptor, rather than relying on whichever window normal routing would choose:

```irc
/exec -wintarget msgwin ls
```

The target window must be valid; the command does not create it.

## Source validation

- Tag `epic5-2.1.2`, `source/irc.c` and `UPDATES`: release identity and historical announcements.
- [source/exec.c](../../source/exec.c): current operation order, fields, defaults, EXEC flags, callbacks, output limits, and incomplete forwarding.
- [source/functions.c](../../source/functions.c) and [source/hook.c](../../source/hook.c): EXECCTL/UUID4 registration and SEND_EXEC type.
- [script/shortener.py](../../script/shortener.py), [script/python](../../script/python), and [script/epic.py](../../script/epic.py): URL collection, loopback service, loading, and listener integration.
