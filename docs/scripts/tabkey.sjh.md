# tabkey.sjh script

The original page contained only a placeholder for `/load tabkey.sjh`. The description below is derived from the retained [script/tabkey.sjh](../../script/tabkey.sjh).

## Synopsis

```text
/load tabkey.sjh
/set tabkey_max_nicks 15
/tkm <target> <message>
/tkm <target> /me <action>
```

The script reloads itself with the preformatted loader when needed and declares package `tabkey.sjh`. It requires `addset` and `servernum` from the usual [builtins](builtins.md) setup; `builtins` loads [addset](addset.md).

## Message-target cycling

This is a remembered-correspondent selector rather than general command, nickname-prefix, or file completion. Loading replaces these key bindings without saving the previous assignments:

| Key | Effect |
| --- | --- |
| Tab (`^I`) | Replace the entire input line with a `/tkm` command for the next remembered target. |
| Control-R (`^R`) | Replace the line with a `/tkm` command while cycling in the reverse direction. |
| Control-X, Control-X (`^X^X`) | Remove the entry at the stored list position, print its name when present, then run forward selection. |

These actions erase the current input line; they do not preserve an unfinished message or send the newly inserted command. The stored position advances during cycling, so the removal binding is not a reliable promise to remove the target currently displayed. With no history, there is no useful target to select.

Incoming/outgoing private messages and private actions add targets using hooks at serial **-12782**. Repeated targets move to the front, and `TABKEY_MAX_NICKS` limits the retained list when a target is added. Its default is **15**; use a positive integer. Lowering it does not immediately trim existing history. The list is global across windows and stores ordinary nicknames as `server-refnum:nickname`.

## Sending to a selected target

`/tkm` uses the target's server prefix, or the current server when none is present, and executes [msg](../commands/msg.md) through [xeval](../commands/xeval.md). If the first message word is literally `/me`, it uses [describe](../commands/describe.md) instead. For example:

```text
/tkm example hello
/tkm 2:example /me waves
```

The second example selects server refnum `2`; it does not create or connect that server. Delivery depends on the connection and normal server rules. Review the selected target before submitting the line, especially after server refnums change.

## Current limitations

The script retains DCC history hooks and `=nickname` handling from an earlier client. DCC is removed from current EPIC6, and those branches do not provide working DCC chat.

Its CHANNEL_NICK handler constructs an updated local list but never assigns that list back to `tc.msglist`. It can attempt to rewrite an input target, but it does not reliably update remembered nicknames after a nick change. The commented-out `401` handler also supplies no automatic removal when a nickname is unavailable.

Package labeling does not save or restore the previous key bindings, setting values, or global history. Other completion scripts can replace these bindings. The file identifies itself only as hop's tabkey script with modifications; neither the source page nor this script establishes an inclusion date.

## Source validation

- [script/tabkey.sjh](../../script/tabkey.sjh): key bindings, history limit, target encoding, nickname-change limitation, and TKM dispatch.
- [script/builtins](../../script/builtins) and [script/addset](../../script/addset): helper and setting creation support.
- [source/hook.c](../../source/hook.c): current MSG, SEND_MSG, ACTION, SEND_ACTION, and CHANNEL_NICK registrations, without the former DCC hooks.
- [source/commands.c](../../source/commands.c): BIND, PACKAGE, MSG, DESCRIBE, and XEVAL registration/dispatch.
- [KNOWNBUGS](../../KNOWNBUGS): retirement of DCC.
