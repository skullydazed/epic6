# subpackage

## Synopsis

```text
/subpackage <name>
```

## Description

`subpackage` chooses a package label relative to the **parent script-loading context**. In a nested [load](load.md), it sets the current file's package name to `<parent-package>::<name>`. Resources defined afterward can then be removed with [unload](unload.md) using that complete label, independently of the parent's resources.

The parent is the immediately enclosing load frame, not the current file's existing [package](package.md) name. For example, when a file loaded by package `suite` executes `subpackage tools`, the result is `suite::tools`, even if that file previously selected a different package. Repeated calls in the same file choose sibling labels relative to the same parent; they do not keep appending to the previous result.

In an outermost loaded file, with no parent load frame, the result is simply `<name>`. In a nested load whose parent has no package name, the result begins with `::`, such as `::tools`.

The command uses all argument text as the suffix, without splitting words, interpreting options, or adding any delimiter besides the nested-load `::`. A missing name is accepted: it selects `<parent-package>::` in a nested load, or an empty package in an outermost load. It does not display the current package. Outside a script-loading context it silently does nothing. Successful changes produce no output or script return value.

## Effects and restrictions

The selected label applies to subsequent package-owned definitions or changes in the current load frame. It does not rename resource symbols or relabel earlier definitions. Nested loads initially inherit the current label; when they finish, the parent's label remains unchanged. See `package` for which aliases, global assignments, hooks, bindings, stubs, and timers receive ownership labels and how updates behave.

The `::` separator is part of the label, not a recursively managed hierarchy. `/unload suite` does not automatically remove resources labeled `suite::tools`; use `/unload suite::tools` for those resources. Unloading removes tagged resources rather than restoring overwritten definitions or reversing every side effect of a script.

Use a single-word name and consistent capitalization. `unload` reads one word; comparisons are case-sensitive for aliases, global assignments, and hooks, and case-insensitive for bindings and timers. Supplying spaces or inconsistent case can make resources difficult to unload together.

## Example

In a parent file loaded with `/load`:

```text
package suite
load suite-tools.irc
```

In `suite-tools.irc`:

```text
subpackage tools
alias suite.tool {echo Tool loaded}
```

The alias is labeled `suite::tools`. After loading, `/unload suite::tools` removes it without unloading resources labeled `suite`.

## History and source

`subpackage` first appeared in EPIC5-0.0.4 and is always registered.

Registration, `subpackagecmd()`, and load-frame package inheritance are in [source/commands.c](../../source/commands.c). Package ownership and unloading are implemented in [source/alias.c](../../source/alias.c), [source/hook.c](../../source/hook.c), [source/keys.c](../../source/keys.c), and [source/timer.c](../../source/timer.c).
