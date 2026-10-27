# Getting started with EPIC

[Welcome](welcome.md) to EPIC, a programmable IRC client with a traditional ircII interface and scripting language.

EPIC may suit you if you want to customize how an IRC client behaves:

- Its own scripting language provides commands, functions, aliases, and event handlers. Optional Python integration is available when enabled in the build. The original page's Perl, Ruby, and Tcl extension list does not describe the current EPIC6 interfaces; Perl and Ruby were retired.
- Scripts can change output, react to events, and define workflows rather than relying only on fixed interface choices.
- It runs in a terminal. Resource use depends on configuration, retained history, scripts, and workload; the original page's general claim of low memory and CPU requirements was not a quantified limit.

Start with the [IRC introduction](introduction.md) and [new-user guide](new_user.md). For customization, see [the scripting introduction](epic_scripting.md), then the [command overview](commands_overview.md) and [function overview](functions_overview.md).

The original page invited users to `#epic` on EFNet. That is retained as the project's historical community contact, rather than a guarantee about current channel activity. The source tree's [README](../../README.md) points to [epicsol.org](https://epicsol.org/) for documentation.

## Source references

- [source/commands.c](../../source/commands.c): current scripting and Python command registration, without Perl, Ruby, or Tcl commands.
- [configure.ac](../../configure.ac) and [source/python.c](../../source/python.c): optional Python integration.
- [UPDATES](../../UPDATES): EPIC6's feature-retirement list includes Perl and Ruby.
