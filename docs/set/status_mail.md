# set status_mail

## Synopsis

```text
/set status_mail [<text>]
/set -status_mail
```

Default: unset. With no text, [set](../commands/set.md) displays the current value. Unsetting makes the field empty.

## Description

Sets the text displayed for `%M` in [status_format](status_format.md), or a per-window format. The C status renderer inserts this text directly; it does not check a mailbox, test whether mail is new, or substitute a count into `%M` inside this setting. The outer format must contain `%M` for the text to appear.

The value is global and is not restricted to the current input window by [show_status_all](show_status_all.md). Changing it rebuilds status formats and requests updates, without enabling mail checking or sending an IRC request.

## Mail-script updates

The supplied [mail](mail.md) implementation is script-provided: `script/mail` loads `mail.py`, requiring EPIC's Python support. On each successful check, the Python script unsets `status_mail` when its combined mailbox count is zero. Otherwise it writes formatted text here, using its `mail_status_format` setting. It tracks MBOX entries and Maildir entries according to its own mailbox rules; the count is not necessarily limited to newly arrived messages.

The script initializes `mail_status_format` to `(Mail: {cur})`. Its Python-format placeholders are `{cur}` for the current count and `{new}` for the count newly recognized on that check. These belong in `mail_status_format`, not in `status_mail`. A later successful check can overwrite manually assigned status text. Turning checking off does not itself clear the last text.

Ordinary EPIC command parsing can expand dollar expressions on assignment. If [status_does_expandos](status_does_expandos.md) is enabled, the assembled status line can additionally expand dollar expressions. Neither mechanism gives `%M` a nested mailbox-count substitution here.

## Examples

Set a fixed marker manually:

```text
/set status_mail Mail available
```

This displays regardless of mailbox contents until changed or overwritten. With the supplied mail script loaded, customize its generated count instead:

```text
/set mail_status_format Mail: {cur}
```

That changes text generated on a later successful check. Hide the current field with `/set -status_mail`; an active checker can populate it again.

## Source references

- [source/vars.c](../../source/vars.c) and [include/config.h](../../include/config.h): string registration, status rebuild callback, and unset default.
- [source/status.c](../../source/status.c): `%M` maps to `status_mail`, which returns the stored text directly.
- [script/mail](../../script/mail) and [script/mail.py](../../script/mail.py): Python loading, mailbox checks, formatting placeholders, status assignments, and clearing behavior.
