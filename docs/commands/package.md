# package

Group resources defined while loading a script under a package name.

## Synopsis

```text
/package name
```

## Description

`package` labels subsequent global [assign](assign.md) variables, [alias](alias.md) commands, [stub](stub.md) definitions, [on](on.md) hooks, [bind](bind.md) bindings and binding actions, and newly created [timer](timer.md) timers with a common package name. These labels allow [unload](unload.md) to remove resources belonging to a package later.

The name applies to definitions or changes made after the command, until the end of the current loaded file or another `package` command. It does not retroactively relabel everything already defined. Updating a tagged global variable, alias, or hook while another package is active can transfer that resource's package association. Updating an existing timer retains its prior package unless changed separately through timer controls.

Package scope follows the [load](load.md) stack. A nested loaded script initially inherits the loading script's current package. It can select a different name for its own definitions without changing the parent's package after it finishes. With no explicit or inherited package, resources defined during loading receive an empty package name, rather than `*`. Local variables are not package-owned globals.

The command uses all supplied argument text as the name, without splitting it into words or interpreting options. With no arguments it sets the current name to empty; it does not list packages. Outside a script-loading context it silently does nothing. Successful changes produce no output, and the built-in is always registered.

Use a simple single-word name with consistent spelling and capitalization. `unload` reads one word, and package comparisons are case-sensitive for global variables, aliases, and hooks but case-insensitive for bindings and timers. A package name is an ownership label, not a namespace prefix for resource names.

## Example

Place this near the start of a file loaded with `/load`:

```text
package greetings
assign greetings.text Hello!
alias greet {echo $greetings.text}
```

After loading that file, `/unload greetings` removes the tagged global variable and alias. Package unloading removes currently tagged resources; it does not restore previous definitions that the script overwrote or reverse other effects such as settings changes, opened files, or IRC requests.

## Related commands and source

[subpackage](subpackage.md) constructs a name relative to the parent loading package. `unload` removes tagged aliases, variables, hooks, bindings, and timers.

Registration, `packagecmd`, `current_package`, and nested-load inheritance are in [source/commands.c](../../source/commands.c). Global variable, alias, and stub tagging and `unloadcmd` are in [source/alias.c](../../source/alias.c). Hook tagging is in [source/hook.c](../../source/hook.c), binding tagging in [source/keys.c](../../source/keys.c), and timer tagging in [source/timer.c](../../source/timer.c).
