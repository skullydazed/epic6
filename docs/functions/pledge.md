# pledge function

## Synopsis

`$pledge(<space-separated promises>)`

`$pledge({"promises":"<promises>","execpromises":"<exec promises>","test":false})`

## Technical

Calls the operating system's `pledge()` interface to restrict future operations of the EPIC process. In the plain form, leading whitespace is skipped and the remaining text is passed as the promises string without word parsing or quote removal. The exec promises argument is left unchanged by passing a null pointer.

Input beginning with `{` selects the JSON form. `promises` sets the current process's promises; `execpromises` sets promises for programs subsequently executed by the process. Omitted string fields remain null pointers, which leave the corresponding operating-system value unchanged. An explicit empty string, or JSON `null`, supplies an empty promises string instead.

## Options

JSON `test: true` checks whether this build has `pledge()` support without calling the syscall or validating the supplied promises. It returns `1` when configured with support and `0` otherwise. The default is `false`.

## Returns

With platform support, a non-test call returns the syscall result: `0` on success or `-1` on error. EPIC does not provide a separate error message or errno value from this wrapper. Without support, a non-test call returns the empty string; the function name remains registered.

Malformed JSON produces a parser diagnostic, but the wrapper does not stop on that failure: it proceeds with its initial null string arguments and false test flag. Use valid JSON rather than relying on a parse error to prevent a syscall.

## Restrictions

Support is enabled when configure finds the platform's `pledge()` function, notably on OpenBSD. Promise keywords and syscall behavior depend on that platform.

Restrictions affect the process beyond the current script or function call and cannot restore previously revoked permissions. Operations outside the promises normally terminate the process; OpenBSD's `error` promise changes violations into operation errors. An empty promises string imposes severe restrictions. Choose promises for all features your client and scripts actually use; there is no universal minimum supplied by this wrapper. See the [OpenBSD pledge manual](https://man.openbsd.org/pledge.2) for supported keywords and their effects.

## Examples

`$pledge({"test":true})` checks build support without changing process restrictions.

`$pledge({"promises":"stdio rpath inet dns tty proc"})` submits that promises list on a supported platform. This is a syntax example, not a complete permissions list for every EPIC configuration.

## Source

Registration, `function_pledge()`, and `parse_kwargs()`: [source/functions.c](../../source/functions.c). Platform detection: [configure.ac](../../configure.ac).
