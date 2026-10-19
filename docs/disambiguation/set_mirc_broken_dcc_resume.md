# set mirc_broken_dcc_resume

## Removed setting

The former setting used this syntax:

```text
/set mirc_broken_dcc_resume [on|off|toggle]
```

It enabled or disabled mIRC-compatible [dcc resume](ctcp_resume.md) negotiation for resuming DCC file transfers.

EPIC6 removes the built-in DCC transfer implementation and does not register `mirc_broken_dcc_resume` as a setting. There is no supported value, default, or resume capability attached to this name. Enabling it cannot restore DCC transfers or mIRC-compatible resume negotiation.

Without script-provided handling of unknown settings, an obsolete assignment such as `/set mirc_broken_dcc_resume on` reports `No such variable "MIRC_BROKEN_DCC_RESUME"`. Remove such assignments from startup scripts. A script accepting the name would supply its own behavior rather than enable the former C implementation.

## Script references

The bundled `autoget` script still contains a check of this setting and an assignment that attempts to turn it on. These are obsolete references to the removed transfer support; they do not provide a replacement setting or resume implementation.

## Source references

- [source/vars.c](../../source/vars.c): current setting registration omits this name and handles unknown-variable errors.
- [source/commands.c](../../source/commands.c) and [source/ctcp.c](../../source/ctcp.c): no built-in DCC transfer command or resume negotiation handler.
- [script/autoget](../../script/autoget): remaining references to the former setting.
- [KNOWNBUGS](../../KNOWNBUGS): records retirement of `/dcc`, `$dccctl()`, and related functionality.
