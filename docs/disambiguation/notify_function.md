# notify function (removed built-in)

## Former synopsis

```text
$notify([on|off serv <server-number>])
```

## Historical behavior

The former built-in function queried the client's internal notify list. Without arguments, it returned all watched nicknames. The `on` selector requested watched names known to be online, and `off` requested those known to be offline; `serv <server-number>` selected the server for that status query. These selectors were function arguments, not invocations of the ON hook-registration command.

Returning a nickname list let scripts manipulate notify information instead of relying on the command's display. Historical examples were:

```text
$notify()
$notify(on serv 2)
$notify(off serv 5)
```

The original setup example used `/notify hop crowman archon sheik` to populate the former list. Those old query forms and results do not describe the current EPIC6 implementation.

## EPIC6 behavior

The C function registry no longer contains `notify`. The supplied [`notify` script](../scripts/notify.md), loaded by [`builtins`](../scripts/builtins.md), defines an alias that can also be called as a function. Its function-call branch is an unfinished placeholder: it immediately returns an empty string without processing the arguments or sending a MONITOR request. It does not return a nickname list, online status, or a success indication.

Do not interpret that empty result as proof that the server's watch list is empty or that nobody is online. Command use is a separate MONITOR wrapper described in [notify command](notify_command.md). It does not supply a replacement client-side cache for the old function. Another loaded script could override the alias and would define its own behavior.

## History

The original DokuWiki revision identifier was `notify_function.txt,v 1.2 2006/08/01 05:32:26 sthalik Exp`. The current supplied MONITOR alias is marked as hop's 2025 implementation.

## Source references

- [source/functions.c](../../source/functions.c): no current built-in `notify` registration.
- [script/notify](../../script/notify): `functioncall()` branch returns without a value or request.
- [script/builtins](../../script/builtins): loads the supplied notify alias.
- [source/commands.c](../../source/commands.c): user-function invocation initializes an empty local return value before executing the alias.
