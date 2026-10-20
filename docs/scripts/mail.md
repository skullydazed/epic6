# mail

## Synopsis

```text
/load python
/load mail
/set mail [on|off|toggle]
/set mail_interval [<seconds>]
/set mail_status_format [<format>]
/check_mail
```

## Description

The bundled `mail` wrapper imports the Python `mail` module, which checks local mailbox sources and displays counts in the status bar. This replaces the older mbox-list script. It no longer defines `mbox add`, `mbox del`, `mbox list`, or `mail_watch_time`, and it does not use a separate built-in mail checker or the retired `on mail` event.

It requires a build with embedded Python support, the bundled `epic` Python module, and the `addset` alias normally supplied by [builtins](builtins.md). [python](python.md) defines `pyload` and adds the script directory to Python's import path; initialize that support before loading `mail`. In the default `global` order, `builtins` attempts its mail load before `python` defines `pyload`, so that earlier attempt does not guarantee successful initialization.

The first successful module import defines `check_mail` and the `set_mail` callback, creates the settings below, and enables checking. A subsequent `/load mail` uses ordinary Python import caching; it does not necessarily rerun module initialization. The checker sends no IRC request and does not connect to IMAP or POP services.

## Settings and checking

| Setting | Initial value | Effect |
| --- | --- | --- |
| [mail](../set/mail.md) | `on` | Enable or disable discovered mailbox readers and the scheduled check timer. |
| [mail_interval](../set/mail_interval.md) | `60` | Seconds between checks. Changing it turns mail off and back on, rediscovers sources, and checks immediately, even if checking was previously off. |
| `mail_status_format` | `(Mail: {cur})` | Python format string for the status text; supports `{cur}` for total counted entries and `{new}` for newly detected identifiers. |

Enabling mail schedules a repeating snap timer named `check_mail` and performs an immediate check. Use a positive integer interval; zero is not a disable switch, since the repeating-timer implementation rejects it while an immediate check can still occur. Use `/set mail off` to stop scheduled checks. Disabling does not clear already displayed mail status or reset the previous comparison sample.

`/check_mail` invokes the installed checker immediately. It does not itself enable disabled readers or schedule another timer. The settings use the script-defined boolean/integer/string interface, not the old numeric mail modes. With no value, `set` displays the current setting.

## Mailbox discovery

Configure locations in the client's process environment before enabling discovery:

- For mbox, nonempty `MAIL` is used if it names an existing file. Otherwise the script looks for a file named by `LOGNAME` under `/var/spool/mail`, `/usr/spool/mail`, `/var/mail`, and `/usr/mail`. If several fallback files exist, the last one found wins. Only one mbox is selected.
- For Maildir, `MAILDIR` must name a directory containing both `new` and `cur` subdirectories. It contributes messages without the `S` flag. Missing required subdirectories produce a local diagnostic and leave that reader unavailable.

Both readers can contribute to the same total. `MAIL` is treated as one filename, not a list of independent mboxes. There is no runtime mailbox-add/delete command in this implementation. Reading local sources requires the client's filesystem permissions; discovery does not validate remote accounts or credentials.

## Counts and output

For each successful check, the readers build a list of identifiers. Maildir uses message keys for messages without `S`. The mbox reader counts every entry and identifies it by a SHA-256 hash of its `From` header, not by a unique message ID or unread status. Several messages with identical sender headers share an identifier, so mbox arrival detection is not reliable per-message identity tracking.

A nonzero sample is compared with the previous nonzero sample. When identifiers not previously present are found, the checker prints `There are N new email(s), T total` directly through Python's EPIC interface. The first nonempty check treats its entries as new. A zero total clears [status_mail](../set/status_mail.md) but does not replace the previous sample, which can affect detection when the same identifiers reappear later.

For a nonzero total, the checker applies `mail_status_format` through Python `str.format` and assigns the result to `status_mail`. That text appears through `%M` when the active status format includes it. Editing `mail_status_format` takes effect on a later successful check; the checker does not print subjects, senders, or one notification per mailbox. It reports identifier changes, not merely mbox modification-time changes.

Mailbox and formatting exceptions are caught and sent to Python logging. A missing or non-ISO-8859-1-compatible mbox `From` header, inaccessible mailbox, or invalid format field can interrupt a check without a clear EPIC success/failure result or status refresh. Check commands define no structured function return value.

## Example

With the desired `MAIL` and/or `MAILDIR` environment configured before starting the client:

```text
/load python
/load mail
/set mail_interval 120
/check_mail
```

This initializes Python support, imports the checker, requests roughly two-minute scheduled checks, and checks immediately again. Ensure `addset` is available first in a configuration that does not load `builtins`. To stop scheduled checks, use `/set mail off`.

## History

The older mbox-list script was written by wd and first appeared in EPIC4-1.1.6. The current wrapper and Python replacement are marked as 2025 work by hop.

## Source references

- [script/mail](../../script/mail) and [script/mail.py](../../script/mail.py): import wrapper, mailbox readers, comparison state, settings, timer, and output.
- [script/python](../../script/python) and [script/epic.py](../../script/epic.py): Python setup, import helpers, and registered aliases.
- [script/builtins](../../script/builtins) and [script/global](../../script/global): supporting load order.
- [source/timer.c](../../source/timer.c): repeating timers, snap timing, and zero-interval rejection.
- [source/status.c](../../source/status.c): `%M` inserts `status_mail` text.
- [configure.ac](../../configure.ac): optional embedded Python build support.
