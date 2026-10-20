# setmail script

## Status in EPIC6

The `setmail` script formerly selected different mail-status formats for the integer `mail` modes `1` and `2`: a simple `(Mail)` indication for the first mode and a count-bearing `(Mail: %M)` format for the second. It allowed each format to be customized separately.

Those numeric mail modes and the old count substitution are no longer the mail-checking interface in EPIC6. The current [mail](../scripts/mail.md) implementation uses a Boolean `mail` setting and formats counts through `mail_status_format` with `{cur}` and `{new}` placeholders. Loading `setmail` does not restore the former modes or mail-count formatter.

## Retained interface

```text
/load setmail
/set mail_format1 <text>
/set mail_format2 <text>
/set mail 1
/set mail 2
```

The retained script still installs system-mode [set](../on/set.md) hooks and initializes ordinary global variables:

| Variable | Initial text |
| --- | --- |
| `mail.format1` | ` (Mail)` |
| `mail.format2` | ` (Mail: %M)` |

`mail_format1` and `mail_format2` are intercepted pseudo-settings, not registered setting definitions. Their hooks store the supplied text in those variables and suppress ordinary setting processing. They do not appear as independently registered variables in a normal setting list, and a no-value query does not implement a normal value display. Loading reapplies the two initial strings.

The `mail 1` hook copies `mail.format1` to `status_mail`; the `mail 2` hook copies `mail.format2` instead. Because the hooks suppress the original operation, these commands do not enable, disable, or select a mode in the current Boolean mail checker. Changing a format variable does not update the displayed status immediately; the corresponding intercepted `mail` command must run again.

## Compatibility limits

Current status rendering returns `status_mail` as text; it does not substitute the old mail count into the `%M` contained in the second format. The current mail checker can overwrite or clear `status_mail` on its next check. Thus the retained hooks can alter a status string, but they do not provide the intended automatic count-aware behavior.

This script does not load a mail checker, schedule checks, read a mailbox, or create a mail-count source. It has no external program requirement or server request, and its hooks have no function-return interface. Other set hooks can replace or interfere with the same patterns. The script has no package declaration or automatic removal of its hooks.

For current mail formatting, configure `mail_status_format` after making the [mail](../scripts/mail.md) implementation available, for example:

```text
/set mail_status_format (Mail: {cur})
```

Use that implementation's `mail on` and `mail off` controls rather than the historical numeric commands. Its Python support and mailbox requirements are described on the mail script page.

## History

The script first appeared in EPIC4pre1.026. The retained source is marked as 1997 work by hop.

## Source references

- [script/setmail](../../script/setmail): retained pseudo-settings, default strings, and numeric-mail interception.
- [source/hook.c](../../source/hook.c): system-mode hooks suppress default handling.
- [source/vars.c](../../source/vars.c): set hooks run before ordinary validation and assignment.
- [source/status.c](../../source/status.c): `status_mail` returns the stored string without the former count substitution.
- [script/mail](../../script/mail) and [script/mail.py](../../script/mail.py): current Boolean mail interface and Python-style count formatting.
