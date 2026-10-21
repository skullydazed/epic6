# Using hooks with serial numbers

Serial numbers divide handlers of the same [on](../commands/on.md) event into independent matching groups. They let several handlers respond to one event without competing for the same single winning match.

## Syntax and order

```text
/on #<noise><event> <serial> "pattern" { commands }
```

The `#` introduces the serial-number form, before the optional noise character. Omit it and the serial argument to use group `0`. Serial numbers can be negative, zero, or positive within the implementation's integer range. Supply ordinary decimal integers; the parser converts the text permissively rather than strictly validating it.

Groups are considered in increasing numeric order: negative groups first, then zero, then positive groups. The numeric gaps are not delays and do not cause empty groups to run. Zero is allowed explicitly, but it has special default-suppression behavior and is not interchangeable with every other group.

## Example

```irc
on #-hook -5 "example *" { echo booya; };
on -hook "example *" { echo foobar; };
on #-hook 5 "example *" { echo yadda yadda yadda; };
hook example hello;
```

The displayed lines from these handlers are:

```text
booya
foobar
yadda yadda yadda
```

The quiet `-` noise mode avoids activation notices. The ordering comes from serial numbers, not definition order. The [hook](../commands/hook.md) command generates a local event; no IRC connection is needed for this example.

## One winning match per group

For each serial number, EPIC compares matching patterns and selects the highest wildcard-match score. Only that group's winning handler runs. Equal scores retain the first encountered match in the hook list; definition order is not a general tie-breaking promise. An excepting handler can win and cause that group to run no body.

Different serial numbers allow independent winners, even with the same pattern. Reusing the same event, pattern, and serial replaces the corresponding existing handler instead of creating an independent second handler. Choose groups deliberately when multiple scripts should coexist, and coordinate their serial numbers rather than assuming a private group namespace for each package.

Patterns and noise modes still follow the ordinary `on` rules. Flexible patterns can be expanded during event dispatch. Earlier handlers can also change state, hook definitions, or the event context used by later processing; serial ordering is not isolation or a transaction.

## Suppressing default behavior

The ordinary noise-mode decision to suppress an event's default action is made only by the winning serial-`0` handler. For example, a `^` handler at a nonzero serial does not automatically suppress default output merely because its noise mode is silent. Its own display behavior can still be affected by that mode.

Likewise, the conditional `?` mode obtains a suppression decision from a function return when the serial-zero handler sets the pending decision. Do not rely on `return 1` from an arbitrary nonzero handler to cancel the event's default behavior. Some event callers ignore suppression results, and advanced [hookctl](../functions/hookctl.md) operations can explicitly alter dispatch; consult the event's page.

## Selecting and removing handlers

Use the same event, serial, and pattern to address the intended handler when changing or removing it. Removing one serial group does not inherently remove all other groups. [On](../commands/on.md) documents listing and removal forms; `hookctl` provides more detailed inspection and control.

Use serial numbers when you need independent handlers, including multiple `on timer` handlers. The `timer` event and the [timer command](../commands/timer.md) are different interfaces: a serial number is not a timer identifier and does not schedule the handler. Do not assume any event must have only nonzero serials; use zero when its default-action control is intended.

## Source references

- `source/hook.c`: serial syntax in `oncmd()`, handler replacement in `add_hook()`, ascending dispatch, best-match selection, and serial-zero suppression.
- `source/reg.c`: wildcard-match specificity scores.
- `source/commands.c`: `hook` event generation and explicit `echo` output.
- `source/timer.c`: timer-command scheduling, separate from hook serials.
