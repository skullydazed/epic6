# set num_of_whowas

## Synopsis

```text
/set num_of_whowas [<number>]
```

Provided by the bundled `builtins` script. Default: `1` when that script initializes the setting. With no number, [set](../commands/set.md) displays the current value. Values must be nonnegative integers.

## Description

Controls the default number of historical nickname records requested by the script-provided [whowas](../commands/whowas.md) alias. It also applies when [auto_whowas](auto_whowas.md) invokes that alias after a no-such-nickname reply.

The alias adds this value as the count argument when no explicit arguments follow the nickname. It does not count or suppress replies locally. The IRC server determines how many records are available and how it interprets the requested count. Zero conventionally requests all available records, but server limits and policy still apply.

For example, after setting the default to three:

```text
/set num_of_whowas 3
/whowas alice
```

The alias sends `WHOWAS alice 3`. An explicit count overrides the default:

```text
/whowas alice 1
```

## Scope and limitations

This is a script setting rather than a registered C built-in variable. Without the script defining it, the name is unavailable unless another script supplies it. The C `whowas` command forwards its arguments without consulting this setting; bypassing aliases with `//whowas alice` therefore does not add the default count.

Changing the value affects future alias invocations, not replies already requested or displayed. It does not enable or disable automatic queries; `auto_whowas` controls that separately. A server can return fewer records, no records, or an error regardless of the chosen value.

## Source references

- [script/builtins](../../script/builtins): defines the integer setting with default one, supplies the `whowas` alias, and invokes it from the `auto_whowas` handler.
- [source/commands.c](../../source/commands.c): the C `whois` handler's `whowas` branch forwards the supplied arguments to the server.
- [source/vars.c](../../source/vars.c): integer setting validation rejects negative and nonnumeric values.
