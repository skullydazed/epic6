# set mail

## Synopsis

```text
/load mail
/set mail [on|off|toggle]
```

This setting is supplied by the bundled Python mail script, not by a C built-in. [Script/mail](../../script/mail) loads [script/mail.py](../../script/mail.py) through `pyload`, requiring a build with Python support and the script's runtime dependencies. Its script-defined boolean setting initializes to `on` when the module loads; the old numeric modes `0`, `1`, `2`, and `3` are not its supported interface.

## Description

Controls the supplied script's local mailbox checking. Enabling it discovers supported mailbox locations, schedules repeated checks, and checks immediately. Disabling it invalidates the mailbox readers and deletes the `check_mail` timer. It does not unregister the setting or the `check_mail` command.

The script defines `mail_interval`, initialized to `60` seconds, and `mail_status_format`, initialized to `(Mail: {cur})`. Changing the interval toggles checking off and on, so it also performs discovery and an immediate check. Loading the script initializes those values and enables checking; it is not a passive query of current mail state.

## Mailbox discovery and counts

The supplied readers support:

- An mbox file named by nonempty `MAIL`, if it exists, otherwise files named by `LOGNAME` under `/var/spool/mail`, `/usr/spool/mail`, `/var/mail`, or `/usr/mail`.
- A Maildir named by `MAILDIR`, requiring both `new` and `cur` subdirectories. This reader counts messages without the `S` flag.

Both readers can contribute to the total. This is runtime discovery, not a mailbox directory fixed at configure time. The script does not connect to IMAP or POP servers.

The mbox reader currently identifies entries by a hash of their `From` header rather than by a unique message ID, while Maildir uses its message keys. The script compares these identifiers with its previous nonempty sample to calculate new arrivals. Counts therefore reflect this implementation rather than a universal unread-mail definition; duplicate sender headers can affect mbox new-message detection.

## Output and limitations

When it finds new identifiers, it prints `There are N new email(s), T total` directly. It updates [status_mail](status_mail.md), used by the `%M` status field, with `mail_status_format` using Python `{new}` and `{cur}` fields. A zero total clears that status text. The current implementation does not reset its previous sample in the zero-total branch.

It does not invoke the removed [on mail](../disambiguation/on_mail.md) hook, list each sender or subject, or provide the former numeric mode for restoring mailbox timestamps. Turning checking off does not itself clear an already displayed mail status. Mailbox or formatting exceptions are caught and sent to Python logging; do not assume every failure produces an EPIC notification.

These checks access local mailboxes and send no IRC request.

## Example

```text
/load mail
/set mail off
/set mail on
```

Loading already enables checking; the latter commands demonstrate disabling and re-enabling it. Configure `MAIL` and/or `MAILDIR` in the process environment before enabling discovery.

## Source references

- [script/mail](../../script/mail): loads the Python implementation.
- [script/mail.py](../../script/mail.py): settings, discovery, readers, comparison, timer, output, and status formatting.
- [source/python.c](../../source/python.c): Python integration and `pyload` support.
- [source/vars.c](../../source/vars.c) and [source/status.c](../../source/status.c): built-in `status_mail` and its `%M` field; no built-in `mail` checker setting.
