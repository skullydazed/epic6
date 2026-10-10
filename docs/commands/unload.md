# unload command

## Synopsis

```text
/unload name
```

## Description

`unload` allows on-the-fly removal of a script's resources when they have been labeled by [`package`](package.md) or [`subpackage`](subpackage.md). Supply the package name, not necessarily the script's filename. The command does not read or delete the script file.

It removes currently tagged global aliases and assignments (including their stub definitions), hooks, binding actions and key bindings, and pending timers. Local variables are not package-owned global assignments. Resources that have since been replaced or relabeled belong to their current package instead.

The name is the first whitespace-delimited argument. Quotes are not removed, extra arguments are ignored, and there are no options or wildcard matching. Match the complete package label: unloading `suite` does not recursively unload `suite::tools`. Use consistent capitalization because aliases, assignments, and hooks compare labels case-sensitively, while bindings and timers compare them case-insensitively.

With no name, the command reports `You must supply a filename for /UNLOAD.` Despite this diagnostic's wording, the argument is a package label. An unknown package is accepted and produces the normal progress messages even if nothing matches.

Before removal, EPIC invokes the unload hook with the package name. A script can use an [`on`](on.md) unload handler to perform additional cleanup, but the hook's return does not cancel the built-in removal. EPIC then reports each resource category being removed and finishes with `Done.` The command does not return a function value.

## Restrictions

Unloading deletes current resources; it does not restore definitions or key bindings that the script overwrote. It does not automatically reverse settings changes, close files or subprocesses, disconnect servers, or undo IRC requests. Such effects require script-specific cleanup. A timer or callback already executing is not interrupted by deleting its pending package resources.

The command is available in every build and requires no IRC connection.

## Example

In a script loaded with [`load`](load.md):

```text
package greetings
assign greetings.text Hello!
alias greet {echo $greetings.text}
```

After loading, remove the tagged variable and alias with:

```text
/unload greetings
```

## Source

[source/alias.c](../../source/alias.c) implements `unloadcmd()` and global alias/assignment removal. [source/hook.c](../../source/hook.c) removes package hooks; [source/keys.c](../../source/keys.c) removes binding actions and key sequences; [source/timer.c](../../source/timer.c) removes matching pending timers. Registration is in [source/commands.c](../../source/commands.c).
