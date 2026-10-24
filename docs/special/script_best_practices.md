# Script best practices

Good EPIC scripts coexist with other scripts, respect the user's theme, and keep event display responsive. Start with these conventions when writing a new script. See [Programming in EPIC](programming.md) for the language basics.

## Use the PF loader

Write scripts for the [PF loader](pf_loader.md), with semicolons between statements and braces around alias and hook bodies. Put the customary loader wrapper at the top of scripts that take no load arguments:

```irc
if (word(2 $loadinfo()) != [pf]) { load -pf $word(1 $loadinfo()); return; };
```

This reloads the file through PF if necessary and returns from the original load. Newlines provide spacing; semicolons separate statements. The wrapper assumes an ordinary single-word filename and does not forward arguments. Scripts with load arguments or unusual filenames should arrange explicit PF loading as described in the loader documentation.

## Allocate serial numbers with getserial()

Use [getserial](../functions/getserial.md) to obtain one or more unused hook serial numbers instead of choosing fixed numbers that another script might already use. Save the results so the script can reuse them across event types and later address its hooks.

```irc
@ myscript.before_serial = getserial(HOOK - 0);
on #-hook $myscript.before_serial "myscript *" {
    @ myscript.last_event = [$*];
};

@ myscript.after_serial = getserial(HOOK + 0);
on #-hook $myscript.after_serial "myscript *" {
    @ myscript.event_count = myscript.event_count + 1;
};
```

The example uses a local `HOOK` event so it can be exercised with `/hook myscript hello` without an IRC connection. Replace `myscript` with your script's own namespace.

`$getserial()` checks existing hooks across all event types, but does not reserve the result. Install a hook using the returned number before requesting another number in the same direction; otherwise repeated calls can return the same number. One saved serial can be used for several event types.

The inline form, such as `/on #^connect + "*" {commands}`, finds an unused serial only for that event type. **There is no direct way to retrieve the serial automatically chosen by `/ON`.** Use `$getserial()` when knowing the number matters.

## Organize hooks around display

Use [serial numbers](serial_numbers.md) to give event processing a clear order:

| Serial | Responsibility |
| --- | --- |
| Negative | Calculate or store information that the display hook needs. |
| Zero | Display the event using the supplied theme support. |
| Positive | Perform work that can happen after display. |

Keep negative hooks limited to preparation that must finish before display. Keep the serial-zero display hook small, using prepared information where needed. Put other processing in positive hooks so it does not delay that event's display. This keeps the display hook's latency as low as possible; expensive positive hooks can still delay subsequent events because they run synchronously.

This ordering assumes an explicit serial-zero display hook. The client's default output and implied hooks run after ordinary hooks, including positive serials. A positive serial alone therefore does not guarantee processing after default output. See [on](../commands/on.md) for default-action and suppression rules.

Serial zero is shared: leave the theme's display hook in place when adding processing hooks. At each serial, only one matching pattern wins. Defining the same event type, serial, and case-insensitive pattern again replaces the existing action and attributes; changing the noise modifier does not create another hook.

## Use the supplied theme support

Use the theme support supplied by the user's scripting environment for event presentation. Keep colors, layout, and message formatting in that display layer so the user's theme controls how events look. Processing scripts should prepare data and let the display layer render it, rather than installing competing display hooks or embedding their own copies of event formatting.

Follow the theme's supported extension and formatting interfaces when adding output. The bundled [colors script](../../script/colors) provides a theme implementation; [loadformats](../scripts/loadformats.md) provides configurable event formats through implied hooks. Their display timing differs: implied formats run after ordinary handlers, so they do not provide the serial-zero display boundary described above.

When a script requires a particular theme or formatting helper, document that dependency and how to load it.
