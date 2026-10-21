# Tcl: unavailable interfaces

The names `tcl` command and `tcl()` function do not identify registered C built-ins in the current EPIC6 source. There is no Tcl integration in the current build configuration and no supplied script replacement. Consequently, there is no current built-in Tcl syntax, result, or interpreter state to use.

This establishes current availability, not a particular retirement date or historical implementation: the repository evidence does not supply an explicit Tcl retirement note. A user-defined alias with this name would have its own behavior.

For native client scripting, see [programming](../special/programming.md), [ircII syntax](../special/ircii_syntax.md), and [expressions](../special/expressions.md). EPIC's language has its own evaluation rules; resemblance to Tcl is not compatibility with a Tcl interpreter.

To run an externally installed Tcl program, use the [exec command](../commands/exec.md) with its interpreter and script filename. The child is a separate local process with explicit input/output, not shared access to client variables, hooks, or aliases. Installing Tcl does not add embedded `tcl` interfaces to EPIC.

## Source references

- `source/commands.c` and `source/functions.c`: current registries have no `tcl` entry.
- `configure.ac`: current build integrations have no Tcl interpreter support.
- `source/exec.c`: separate-process execution.
