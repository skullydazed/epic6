# set mail_type

## Removed setting

The former setting used this syntax:

```text
/set mail_type [mbox|maildir]
```

It selected how the former built-in mail checker interpreted the user's maildrop: an mbox file associated with `MAIL`, or a Maildir associated with `MAILDIR` and its `new` directory.

EPIC6 does not register `mail_type` as a built-in setting, and the supplied current mail script does not read it. There is no supported `mbox` or `maildir` selection, default, or activation behavior attached to this name now.

Without script-provided handling of unknown settings, an obsolete assignment such as this follows the ordinary unknown-variable path:

```text
/set mail_type mbox
```

It reports `No such variable "MAIL_TYPE"`. Remove such assignments from startup scripts. Defining a similarly named setting in another script does not change the supplied checker's discovery rules unless that script explicitly implements the behavior.

## Current mail discovery

The Python implementation loaded by [mail](../set/mail.md) installs both mbox and Maildir readers. They can both contribute to its counts; there is no exclusive type selector.

- The mbox reader uses an existing file named by `MAIL`, or searches common spool directories using `LOGNAME`.
- The Maildir reader uses `MAILDIR` when it names a directory with both `new` and `cur` subdirectories. It counts messages without the seen flag rather than simply counting the `new` directory alone.

These are process-environment inputs used when checking is enabled. With a Python-enabled setup and appropriate mailbox environment:

```text
/load mail
```

The supplied script initializes and enables its [mail](../set/mail.md) boolean setting, performs discovery, and schedules checks. See that page for count semantics and limitations. This local mailbox work sends no IRC request.

## Source references

- [source/vars.c](../../source/vars.c): current registration omits `mail_type` and handles unknown-variable errors.
- [script/mail](../../script/mail): loads the Python checker through `pyload`.
- [script/mail.py](../../script/mail.py): installs both reader factories and discovers their environment-selected maildrops without consulting a type setting.
