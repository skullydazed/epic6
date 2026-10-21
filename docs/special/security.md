# Security issues

EPIC's flexibility lets scripts run local commands, handle incoming messages, start processes, and access files. A script runs with the permissions of the EPIC process. Loading a script is executing code, not merely importing display preferences, and hooks are not a sandbox.

## Event handlers can turn messages into commands

[On](../commands/on.md) handlers run code in response to matching events. An unsafe handler can treat text received from another user as an EPIC statement. For example, this pattern of code should not be installed:

```irc
on ^msg "% obey *" {
    $2-;
};
```

For a matching private-message event, the nickname is the first argument and message words follow it. This body evaluates the message text after `obey` as a command. It does not restrict the sender or the command: someone able to trigger the event can choose what EPIC attempts to execute.

The `^` hook noise mode suppresses the normal triggering-message display at the ordinary serial group. It does not make arbitrary commands safe, and output or other effects of those commands follow their own rules. Other hooks can also affect whether this event path runs, so the example is not a guarantee that all messages reach this handler.

Keep incoming data as data. A handler that only displays text can call `echo` with that text, rather than putting it in the command-name position. If automation needs a remote trigger, define a small fixed set of actions, validate their arguments, and establish identity using the network's documented mechanisms; a familiar nickname alone does not authenticate a person.

## Understand commands before installing them

Social engineering can involve asking someone to paste a command, load a script, or disable a setting. Read the full command and any code it installs. An innocuous-looking hook may run later, after the conversation that installed it has been forgotten.

[Exec](../commands/exec.md) can start programs through a shell or directly, depending on its options and configuration. File functions can read, write, or delete files. A script does not need to use `exec` to have significant local effects. The current implementation does not provide a general safety setting that makes arbitrary hooks or scripts harmless.

Command aliases and optional scripts can change what a name does. The C dispatcher uses complete names, while the bundled `ambig` script can expand unique prefixes. Prefer full documented commands when reviewing instructions, and inspect any same-named alias involved. See [new user](new_user.md) and [programming](programming.md).

## Expansion and later evaluation

[Expando substitution](expando_substitution.md) and [deferred evaluation](quoting_hell.md) can introduce multiple parsing stages. Text displayed safely in one context is not automatically safe when later passed to `eval`, used as a command name, embedded in a shell command, or saved as executable callback text.

Braces help control when a body expands; they do not validate its contents or authenticate the source of a substituted value. Check the receiving command's argument rules and keep the operation fixed where possible. Do not assume that quoting rules from a shell, regular expression, or one EPIC command apply to another.

## Cleanup and visibility

Inspect [on](../commands/on.md), [alias](../commands/alias.md), [timer](../commands/timer.md), and [bind](../commands/bind.md) state when investigating unexpected behavior. A script can install several kinds of resources. [Unload](../commands/unload.md) removes matching package-tagged resources but does not universally reverse prior requests, file changes, external processes, or untagged state.

If you are unsure what a supplied command does, use the documentation and ask for an explanation before running it. This is especially important for code that responds automatically to future network input.

## Source references

- `source/parse.c`: private-message hook arguments and default display path.
- `source/hook.c`: handler selection, noise/suppression modes, and callback execution.
- `source/commands.c`: expanded command dispatch, loading, and deferred execution.
- `source/exec.c`: shell and direct process execution.
- `source/alias.c`: aliases, variables, package tagging, and unloading.
- `script/ambig`: optional unique-prefix command expansion.
