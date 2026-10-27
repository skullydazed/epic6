# EPIC news archive

See the [UPDATES](../../UPDATES) file shipped with the source for development documentation that may not yet appear elsewhere. The entries below preserve announcements from 2014–2016; they are historical release notes, not a current release list or a statement that every mentioned feature remains in EPIC6.

## 2016-02-13: EPIC5 2.0 announcement

The original page announced EPIC5 2.0 on February 13, 2016. The repository's `epic5-2.0` release record is dated February 12, 2016, and its `internal_version` is `20160212`; the announcement date is preserved separately from that source release date.

## 2014-04-13: EPIC5 1.1.10

EPIC5 1.1.10's source release date is April 13, 2014. Its release commit, `8390b7ef`, records internal commit ID **1781**, correcting the original page's 1798.

The original announcement reported that EPIC5 1.1.9 had been withdrawn because of a serious bug. Its highlighted changes were:

- `$encodingctl()` supplied low-level control over `/encoding` rules.
- `$fix_width()` was rewritten to match its intended behavior.
- Most string-manipulation functions became UTF-8 aware, counting code points rather than bytes where applicable.
- `/xtype -L`, `$[num]var` padding/truncation, and `/fec` gained UTF-8 handling.
- Case-insensitive comparisons and matching, including `==` and `$rmatch()`, gained Unicode-aware behavior subject to locale support.
- The scripting environment's UTF-8 support was substantially expanded.

These were release highlights rather than guarantees that every string operation counted display columns or handled every Unicode text operation identically.

## 2014-04-04: EPIC5 1.1.8

EPIC5 1.1.8, internal commit ID 1775, was released on April 4, 2014. The announcement highlighted:

- UTF-8 support for the console, scripts, and IRC messages.
- Encoding conversion for messages sent to and received from IRC.
- 256-color support.
- UTF-8-aware string functions such as `$mid()` and `$split()`.
- Additional improvements recorded in the source history.

The original phrase “unlimited” recoding should not be read literally: available encodings and usable conversions depend on the system and EPIC's conversion rules.

## 2014-03-31: EPIC4 2.10.5

The archived announcement records EPIC4 2.10.5, internal commit ID 779, released March 31, 2014, adding full Maildir support contributed by Jari Turkia. This is an EPIC4 announcement retained from the original page; it is not independently established by the EPIC5/EPIC6 release records inspected here.

## 2014-02-11: EPIC4 2.10.4

The archived announcement records EPIC4 2.10.4, internal commit ID 778, released February 11, 2014. Its listed changes were a new `unicode` script from |Rain| and a fix for `$(IP)` support taken from EPIC5 for FreeBSD porters. These EPIC4 details are retained as the original announcement's historical claims.

## Source references

- [UPDATES](../../UPDATES): current development notes.
- Repository tag `epic5-2.0`, `source/irc.c`: version name, internal release date, and commit ID; tag commit `075087c1` records the release.
- Repository commit `8390b7ef`, `source/irc.c` and `UPDATES`: EPIC5 1.1.10 release identity and contemporary encoding/function changes.
- Repository commit `d0ac3ed1`, `source/irc.c`: EPIC5 1.1.8, release date `20140404`, and commit ID 1775.
- Repository commits `1ed00810`, `66d46a70`, `82e6832d`, and `7aa00123`: Unicode case handling, string functions, `encodingctl()`, and `fix_width()` changes leading to those releases.
