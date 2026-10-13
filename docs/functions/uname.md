# uname

## Synopsis

`$uname([<description>])`

## Technical

Returns system information about the machine on which EPIC is running, obtained from the operating system's `uname` interface. The entire argument is a description template, including spaces and literal quotation marks. With no input, the template defaults to `%s %r`.

Template selectors are case-sensitive:

| Selector | Replacement |
| --- | --- |
| `%m` | Machine architecture. |
| `%n` | Node name or hostname reported by the operating system. |
| `%r` | Operating-system release. |
| `%s` | Operating-system name. |
| `%v` | Operating-system version. |
| `%a` | The fields `%s %n %r %v %m`, separated by spaces. |
| `%%` | A literal percent sign. |

Unknown selectors retain both characters, so `%S` remains `%S`. A trailing percent sign remains a percent sign. Ordinary text is copied without word parsing. Replacement field text is not scanned again for selectors.

## Returns and restrictions

The expanded description, or the literal string `unknown` if the system call fails. Empty input selects the default format rather than returning empty. Values and their wording depend on the operating system; the architecture field is not necessarily a detailed CPU model.

Output is bounded by EPIC's 2048-byte formatting buffer. Long templates can stop early, and large field substitutions can be truncated; truncation can split UTF-8 text. There are no options, settings, or server requirements. This queries the local host, not the IRC server.

## Examples

```irc
$uname(This machine (%n) is a %m running %s %r)
$uname()
$uname(%a)
```

The first expression could return `This machine (paradigm) is a i686 running Linux 2.2.0`. The second returns the system name and release; actual values depend on the host.

## Source

Handler and selectors: [`source/functions.c`](../../source/functions.c:6324). Buffer size: [`include/irc.h`](../../include/irc.h:80).
