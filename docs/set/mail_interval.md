# set mail_interval

## Synopsis

```text
/load mail
/set mail_interval [<seconds>]
```

This integer setting is supplied by the bundled Python [mail](mail.md) implementation, not a C built-in. Loading [script/mail](../../script/mail) uses `pyload` to install [script/mail.py](../../script/mail.py), requiring Python integration and its runtime dependencies. The script initializes the interval to `60` seconds and enables checking. With the setting installed, omitting the value queries it.

## Description

Controls how often the supplied mail script checks its discovered local mbox and Maildir sources. It creates a repeating snap timer named `check_mail`; the interval is in seconds, with the default intended to align checks near minute boundaries. Checks run through the client's event loop, so exact timing is not guaranteed.

Changing the interval executes `set mail off` followed by `set mail on`. This deletes the old check timer, rediscovers mailbox sources, schedules a new timer, and performs an immediate check. It also **enables checking even if mail was previously off**. Set `mail off` afterward if you only wanted to change the value for later use.

Use a positive integer interval. Zero is not the mail script's disable switch: the repeating timer implementation rejects intervals below 0.01 seconds, and the script's immediate check can still run after a failed timer request. Use `/set mail off` to disable scheduled checks rather than relying on an invalid interval.

## Status and scope

The script caches results in [status_mail](status_mail.md), formatted through its `mail_status_format` setting. Redrawing the status bar does not itself recheck mail. A manual `/check_mail` invokes the installed script checker independently of waiting for the next timer.

The current implementation has no CPU Saver override. This setting does not change the clock interval or unrelated timers. The script reads local mailboxes; it does not poll remote IMAP or POP services or send an IRC request. See [mail](mail.md) for discovery, count semantics, and failure reporting.

## Example

Check local mail about every two minutes:

```text
/load mail
/set mail_interval 120
```

Loading and changing the interval both enable checking. Stop it with:

```text
/set mail off
```

## Source references

- [script/mail](../../script/mail): loads the Python implementation.
- [script/mail.py](../../script/mail.py): defines the interval callback, repeats the `check_mail` timer, checks immediately on enable, and updates cached status text.
- [source/timer.c](../../source/timer.c): repeating-timer validation and snap scheduling.
- [source/ircaux.c](../../source/ircaux.c): interval-boundary calculation used for snap timers.
- [source/vars.c](../../source/vars.c): current built-in variable table does not register `mail_interval`.
