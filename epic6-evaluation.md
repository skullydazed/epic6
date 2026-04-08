# EPIC6 IRC Client — Evaluation for the Unix Power User

## TL;DR

EPIC6 is an excellent fit for a Unix power user who wants a highly configurable, terminal-based IRC client and is willing to learn its own scripting language (or leverage embedded Python). It has a 30-year lineage, is under active development, and trades legacy cruft for focused modern features. If you are comfortable in a terminal and you enjoy tinkering, it will reward you generously.

---

## Background & Heritage

EPIC (**E**nhanced **P**rogrammable **I**RC **C**lient) traces its roots to ircII (1990) and has been continuously developed since its first release in November 1994. The EPIC6 branch was forked from EPIC5 in August 2024 and is actively maintained as of early 2026, with dozens of commits and new features added throughout 2024–2025. With roughly 94,000 lines of C code it is lean yet feature-complete.

The version lineage reads: ircII → EPIC1 (1994) → EPIC2 → EPIC3 → EPIC4 → EPIC5 (2006–2016) → **EPIC6 (2024–present)**.

---

## Scripting Language — The Primary Customisation Vehicle

EPIC6's own scripting language ("ircII script") is the heart of its configurability. It is a macro/command language embedded in the client with:

* **260+ built-in `$function()` calls** covering string manipulation, list/word operations, math, regex, time/date formatting, cryptography (ECDSA), JSON, network connections, server/window introspection, and more.
* **`/alias`** — define arbitrary commands with typed arguments, default values, `void` checks, and local scoping via packages.
* **`/on <hook>`** — event-driven callbacks for every IRC event (JOIN, PART, MSG, NOTICE, CTCP, numerics, connect/disconnect, window switches, timers, etc.). Hooks support priority ordering and pattern matching.
* **`/set`** and **`/addset`** — hundreds of tunable settings; you can also add your own with `/addset`.
* **`/bind`** — bind any key or key sequence to any action.
* **`/timer`** — schedule one-shot or repeating callbacks.
* **`/if`, `/while`, `/fe` (foreach)** — full flow control.
* **`/queue`** — command queues for rate-limited or sequential actions.
* **`/exec`** — pipe data to/from external Unix commands; integrates cleanly with the rest of the script environment.

The included `script/epicrc.example` demonstrates a complete working configuration: custom status bar, window management aliases (`wj`, `wjj`, `wnh`…), highlight tracking with file logging, message windows, per-network routing, and custom keybindings — all in idiomatic EPIC script.

### Learning Curve

The scripting language has a syntax unlike anything you already know (not quite shell, not quite C, not quite Tcl). There is a learning curve. The official documentation lives at [https://epicsol.org/](https://epicsol.org/). Once past the basics, it is expressive and consistent.

---

## Python Integration

If you prefer Python over ircII script, EPIC6 ships a full embedded Python interpreter:

* `/python <statement>` — run a Python statement.
* `$python(<expression>)` — evaluate a Python expression from within ircII script.
* `script/epic.py` — a high-level Python module exposing `@alias('name')` and `@on('hook')` decorators so you can write EPIC aliases and hook handlers as ordinary Python functions.
* Access to all EPIC internals (send commands, query state, echo to windows) from Python.

This is a genuine first-class integration — not a subprocess bridge. You can write complex bots or client extensions entirely in Python.

**Note:** Ruby and Perl support were deliberately removed in EPIC6. Python is the sole non-native scripting language going forward.

---

## Security & Modern Protocol Support

| Feature | Status |
|---|---|
| TLS/SSL | **Mandatory** — cannot be compiled out; requires OpenSSL |
| SASL PLAIN | ✅ via `/load sasl` |
| SASL EXTERNAL (cert) | ✅ via `/load sasl` |
| SASL ECDSA-NIST256P-CHALLENGE | ✅ with built-in `$ecdsatool()` for key management |
| SASL SCRAM-SHA-512 | ✅ added May 2025 |
| IRCv3 CAP negotiation | ✅ automatic, scriptable via `/on cap` |
| IRCv3 CAP hold/release | ✅ for fine-grained connection sequencing |

EPIC6 embeds OpenSSL and the ECDSA key tool (`ecdsatool.c`) directly — no external tooling required for certificate auth.

---

## Window & UI System

* Multiple simultaneous windows, each with its own level filter, channel set, server association, and scroll buffer.
* Windows can be visible/hidden, split, swapped, pinned, or made non-swappable.
* **Fully configurable status bar** via `%`-format tokens — custom fields, network names, channel, lag, modes, user-defined fields.
* **Mouse support** (click-to-focus, scroll wheel) via `/set mouse on` — opt-in so it doesn't break text selection.
* `scrollback` and `lastlog` sizes configurable up to 65,536+ lines.
* Output routing is deterministic and documented (`doc/set_context`).
* **Level-based routing**: every IRC event has a level (MSG, PUBLIC, NOTICE, JOINS, etc.); windows declare which levels they display.

---

## What Was Removed in EPIC6 (Intentionally)

The author explicitly values *focus over bloat*. Removed features:

* Ruby, Perl (Python is the one supported external language)
* Blowfish, FiSH, SED, CAST5 encryption (E2E encryption is planned to return)
* Flood control (the old built-in mechanism)
* CPU Saver
* Shift-JIS, termcap-only mode
* C90-only build support
* ircII's original math parser (replaced by a better one)
* Unix domain socket server connections
* `/REDIRECT` and `/FLUSH` (most users never used them)

Some features moved from hardcoded C into scripts (mail checking, `/NOTIFY`). DCC and `/ignore` are temporarily absent but planned to return.

If you depended on FiSH/Blowfish channel encryption or Perl scripting from EPIC5, you will need to find alternatives.

---

## Included Script Library

The `script/` directory ships 70+ ready-to-load scripts covering:

`sasl`, `reconnect`, `notify`, `ignore`, `highlight`, `colors`, `tabkey` (tab completion, multiple variants), `history`, `netsplit`, `floodprot`, `dcc_ports`, `layout`, `screen`/`tmux` integration, `paste`, `nopaste`, `url.irc`, `shortener.py`, `mail.py`, `logman`, `ctcp`, `chanmonitor`, `massmode`, `ban`, `autoget`, `autojoin`, `nickcomp`, `rejoin`, `scan`, `topicbar`, and more.

This is a substantial head start for any power user.

---

## Building & Installation

Requires a POSIX 1003.1-2017-compliant system (Linux or BSD released ~2020+). Standard autoconf build:

```sh
./configure [--with-ssl=PATH] [--with-installtype=prod]
make
make install
```

`--with-installtype=prod` installs as `epic6` without version suffix — recommended for daily use.

OpenSSL is the only mandatory external dependency. Python 3 is optional (needed for Python scripting).

---

## Verdict

| Criterion | Score |
|---|---|
| Terminal / Unix-native | ✅ Excellent — born here, lives here |
| Configurability (keybindings, status, windows) | ✅ Excellent |
| Scriptability (native language) | ✅ Excellent — 260+ functions, full event system |
| Scriptability (Python) | ✅ Good — full embedding with high-level API |
| Modern IRC protocol support (TLS, SASL, IRCv3 CAPs) | ✅ Good and improving |
| Active maintenance | ✅ Yes — regular commits through 2025 |
| Documentation | ⚠️ Adequate — online at epicsol.org; no local docs besides `doc/` and `UPDATES` |
| DCC file transfer | ⚠️ Temporarily absent; planned to return |
| E2E encryption | ⚠️ Temporarily absent; planned to return |
| Perl/Ruby scripting | ❌ Removed permanently |
| Initial learning curve | ⚠️ Steep — ircII script syntax is unfamiliar |

**Recommendation:** If you are a Unix power user who is comfortable in a terminal, willing to learn a new scripting language (or write Python), and prioritise deep customisation over out-of-the-box ease, EPIC6 is an excellent choice. It is the most scriptable terminal IRC client in active development today. Be aware that DCC and built-in ignore/encryption are works-in-progress — if you need those *today*, stay on EPIC5 and migrate to EPIC6 when those features land.
