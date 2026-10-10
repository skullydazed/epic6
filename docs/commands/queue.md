# queue

Store command blocks in named queues for later batch execution.

## Synopsis

```text
/queue
/queue name {commands}
/queue -expand_now name {commands}
/queue -do [-no_flush] name
/queue -runone name
/queue -delete name entry-number
/queue -flush name
/queue -list name
/queue -show
/queue -help
```

## Description

`queue` provides batch processing: prepare commands now and execute them at a later, undetermined time. Each named queue holds an ordered list of command blocks, appended at the end and normally executed from the beginning. Names are case-insensitive and must be single tokens. There is no explicit limit on queue or entry count beyond available memory.

Every added entry must be enclosed in braces, even if it contains only one command. A block can contain multiple statements. A missing closing brace produces a diagnostic; trailing text after a complete braced body is ignored. The queue may already have been created before an invalid body is rejected, leaving an empty queue.

Without arguments, `queue` lists queue names and entry counts. A name alone performs no action; use `-list` to inspect its contents. Adding a block reports its new entry count. Queue operations do not return a value, and missing queues normally produce a diagnostic except for `-flush` and `-runone`, which silently do nothing.

## Options

| Flag | Effect |
| --- | --- |
| `-do` | Run every entry in order, then remove the queue by default. |
| `-no_flush` | With `-do`, retain the queue and its entries for reuse. |
| `-runone` | Execute the first entry, then remove that entry; retain the queue, even when empty. |
| `-delete` | Remove the specified entry without running it. Entries are numbered from 1. |
| `-flush` | Remove the whole queue without executing it. |
| `-list` | Display the named queue's numbered command blocks and end marker. |
| `-show` | Display contents of all queues and return immediately. |
| `-expand_now` | Expand the block before storing it. |
| `-help` | Display usage and return immediately. |

Flags are case-insensitive and may use either `-` or `/` as their prefix. Recognition uses the initial letter except that `-do` and `-delete` require their first two letters and `-runone` its first four. Use the full spellings to avoid confusion. Flags and the name can appear before the body, but encountering an entry number ends flag parsing. Unknown flags produce a diagnostic. Avoid combining unrelated operations.

A missing deletion number produces an error. Zero and positive numbers beyond the list silently remove nothing. Use positive entry numbers; other negative values are not safely validated. Removing an entry renumbers subsequent entries in listings.

## Expansion and execution

Each entry stores both its command text and a copy of the argument context (`$*`) present when it was queued. On execution, it runs in a fresh named command scope with that saved argument context. It uses the current client/server state at execution time, rather than preserving the original window or server context. Execution-time arguments are not appended by `-do` or `-runone`.

Normally the stored block undergoes ordinary expansion when it runs. `-expand_now` performs an additional expansion before storage using the enqueueing context; surviving dollar expressions can still expand at execution time. This is not a guarantee that the resulting text will never be expanded again.

The queue is not detached or copied before execution. `-do` traverses the live list, so entries appended while it runs can be executed during that same run and then flushed. `-runone` removes its first entry only after the block finishes. Do not recursively execute, flush, or delete entries from the queue currently running: the implementation does not guard against those mutations, and they can invalidate its traversal. Use a separate queue for work created by an executing block.

When flags are combined, a supplied body is appended first, then operations occur in this fixed order: `-runone`, `-do`, `-delete`, `-list`, and finally flushing. `-do` enables flushing unless `-no_flush` is present; an explicit `-flush` still flushes. Prefer separate commands when ordering matters.

## Examples

Add a two-statement block to queue `foo`, then inspect it:

```text
queue foo {echo This is a queued command.; echo Isn't it cool?}
queue -list foo
```

Execute and remove that queue:

```text
queue -do foo
```

Remove entry 2 from an existing queue with multiple entries:

```text
queue -delete foo 2
```

## Restrictions and source

The built-in is always registered. Storing and inspecting queues need no IRC connection; queued commands have their own requirements and side effects. Execution is synchronous and is not automatically scheduled. See [timer](timer.md) and [defer](defer.md) for other delayed-execution mechanisms.

Registration is in [source/commands.c](../../source/commands.c). Parsing, storage, listing, deletion, and execution traversal are in [source/queue.c](../../source/queue.c); command-scope execution and expansion are in [source/commands.c](../../source/commands.c) and [source/alias.c](../../source/alias.c).
