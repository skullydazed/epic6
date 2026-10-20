# Functions

EPIC functions accept arguments and return a value. They can manipulate strings and word lists, perform calculations, inspect client state, or perform operations such as file I/O. A function call can have side effects; it is not necessarily a read-only query.

For a broader introduction, see [programming](programming.md). See [expressions](expressions.md) and [expando substitution](expando_substitution.md) for how function results fit into statements.

## Calling functions

In expanded text, write `$name(arguments)`. In an expression, write `name(arguments)` without the dollar sign. Arguments are text expanded before the call, usually separated by spaces rather than commas; each function defines its own argument and quoting rules. Empty parentheses supply an empty argument string.

```irc
alias measure { echo Length: $strlen($*) }
@ amount = abs(-12)
```

After defining the alias, `/measure hello` displays `Length: 5`. The expression assigns `12` to `amount`. Do not assume that a function's argument list is an arithmetic expression: use `${...}` explicitly when an argument needs calculation.

Names are case-insensitive. An ordinary call prefers a C built-in when a script alias has the same name. `$:name(arguments)` selects the script alias; `$::name(arguments)` selects the built-in. If the selected function does not exist, the dispatcher returns an empty string. Empty results can also be valid data or function-specific failures, so consult the individual page before treating an empty result as a success or error indicator.

Scripts define functions using aliases, optionally with an [argument list](arglist.md). `return value` supplies their result; a function alias starts with an empty return value. The [functions script](../scripts/functions.md) is a collection of helpers, not a prerequisite for calling C built-ins.

## Functions by domain

The groups below provide navigation through the current interfaces. Each linked page defines the actual arguments, return values, errors, side effects, and requirements. Similar names do not imply identical behavior. In particular, string positions, Unicode code points, display widths, and word indices are different units.

### Extracting substrings from strings

Extract, trim, or locate portions of text.

[after](../functions/after.md), [before](../functions/before.md), [chop](../functions/chop.md), [left](../functions/left.md), [leftpc](../functions/leftpc.md), [mid](../functions/mid.md), [rest](../functions/rest.md), [right](../functions/right.md).

### String operations

Transform text, inspect characters, and manage formatting.

[ascii](../functions/ascii.md), [asciiq](../functions/asciiq.md), [center](../functions/center.md), [chr](../functions/chr.md), [chrq](../functions/chrq.md), [count](../functions/count.md), [cparse](../functions/cparse.md), [deuhc](../functions/deuhc.md), [fix_width](../functions/fix_width.md), [getopt](../functions/getopt.md), [hex](../functions/hex.md), [index](../functions/index.md), [insert](../functions/insert.md), [is8bit](../functions/is8bit.md), [isalpha](../functions/isalpha.md), [isdigit](../functions/isdigit.md), [isnumber](../functions/isnumber.md), [isutf8](../functions/isutf8.md), [joinstr](../functions/joinstr.md), [maxlen](../functions/maxlen.md), [msar](../functions/msar.md), [nohighlight](../functions/nohighlight.md), [numlines](../functions/numlines.md), [pad](../functions/pad.md), [pass](../functions/pass.md), [printlen](../functions/printlen.md), [repeat](../functions/repeat.md), [reverse](../functions/reverse.md), [rgb](../functions/rgb.md), [rindex](../functions/rindex.md), [rsubstr](../functions/rsubstr.md), [sar](../functions/sar.md), [shiftbrace](../functions/shiftbrace.md), [split](../functions/split.md), [splitw](../functions/splitw.md), [stricmp](../functions/stricmp.md), [strip](../functions/strip.md), [stripansi](../functions/stripansi.md), [stripansicodes](../functions/stripansicodes.md), [stripc](../functions/stripc.md), [stripcrap](../functions/stripcrap.md), [strlen](../functions/strlen.md), [strtol](../functions/strtol.md), [substr](../functions/substr.md), [tolower](../functions/tolower.md), [toupper](../functions/toupper.md), [tr](../functions/tr.md), [unicode](../functions/unicode.md), [unsplit](../functions/unsplit.md), [xform](../functions/xform.md).

### Word operations

Extract and manipulate word lists; some functions change a named variable.

[afterw](../functions/afterw.md), [beforew](../functions/beforew.md), [chngw](../functions/chngw.md), [common](../functions/common.md), [diff](../functions/diff.md), [findw](../functions/findw.md), [findws](../functions/findws.md), [fromw](../functions/fromw.md), [indextoword](../functions/indextoword.md), [insertw](../functions/insertw.md), [leftw](../functions/leftw.md), [midw](../functions/midw.md), [notw](../functions/notw.md), [numsort](../functions/numsort.md), [numwords](../functions/numwords.md), [pop](../functions/pop.md), [prefix](../functions/prefix.md), [push](../functions/push.md), [qword](../functions/qword.md), [remw](../functions/remw.md), [remws](../functions/remws.md), [restw](../functions/restw.md), [revw](../functions/revw.md), [rightw](../functions/rightw.md), [shift](../functions/shift.md), [sort](../functions/sort.md), [sortby](../functions/sortby.md), [splice](../functions/splice.md), [tow](../functions/tow.md), [uniq](../functions/uniq.md), [unshift](../functions/unshift.md), [word](../functions/word.md), [wordtoindex](../functions/wordtoindex.md).

### Math functions

Numeric calculations and conversions; domain and range behavior can depend on the platform math library.

[abs](../functions/abs.md), [acos](../functions/acos.md), [acosh](../functions/acosh.md), [asin](../functions/asin.md), [asinh](../functions/asinh.md), [atan](../functions/atan.md), [atanh](../functions/atanh.md), [ceil](../functions/ceil.md), [cos](../functions/cos.md), [cosh](../functions/cosh.md), [exp](../functions/exp.md), [floor](../functions/floor.md), [jn](../functions/jn.md), [log](../functions/log.md), [log10](../functions/log10.md), [rand](../functions/rand.md), [sin](../functions/sin.md), [sinh](../functions/sinh.md), [srand](../functions/srand.md), [tan](../functions/tan.md), [tanh](../functions/tanh.md), [tobase](../functions/tobase.md), [trunc](../functions/trunc.md), [yn](../functions/yn.md).

### Time info

Timestamps, date formatting and parsing, and elapsed-time formatting.

[metric_time](../functions/metric_time.md), [mktime](../functions/mktime.md), [stime](../functions/stime.md), [strftime](../functions/strftime.md), [strptime](../functions/strptime.md), [tdiff](../functions/tdiff.md), [tdiff2](../functions/tdiff2.md), [time](../functions/time.md), [utime](../functions/utime.md).

### Pattern matching

Wildcard selection, filename globbing, and regular-expression handles.

[cofilter](../functions/cofilter.md), [copattern](../functions/copattern.md), [corfilter](../functions/corfilter.md), [corpattern](../functions/corpattern.md), [filter](../functions/filter.md), [glob](../functions/glob.md), [mask](../functions/mask.md), [match](../functions/match.md), [pattern](../functions/pattern.md), [regcomp](../functions/regcomp.md), [regcomp_cs](../functions/regcomp_cs.md), [regerror](../functions/regerror.md), [regexec](../functions/regexec.md), [regfree](../functions/regfree.md), [regmatches](../functions/regmatches.md), [rfilter](../functions/rfilter.md), [rmatch](../functions/rmatch.md), [rpattern](../functions/rpattern.md).

### Server operations

Inspect or control known server connections. Most information queries read cached client state; a control operation can initiate activity.

[cipher](../functions/cipher.md), [isaway](../functions/isaway.md), [isconnected](../functions/isconnected.md), [isencrypted](../functions/isencrypted.md), [myservers](../functions/myservers.md), [serverctl](../functions/serverctl.md), [serverwin](../functions/serverwin.md), [servports](../functions/servports.md), [ssl](../functions/ssl.md), [usermode](../functions/usermode.md), [version](../functions/version.md).

### Channel info

Inspect locally known channel state. Results depend on the selected server and information received from it.

[chankey](../functions/chankey.md), [chanlimit](../functions/chanlimit.md), [chanmode](../functions/chanmode.md), [channel](../functions/channel.md), [chanusers](../functions/chanusers.md), [chansyncing](../functions/chansyncing.md), [chops](../functions/chops.md), [ischannel](../functions/ischannel.md), [ischanop](../functions/ischanop.md), [ischanvoice](../functions/ischanvoice.md), [iscurchan](../functions/iscurchan.md), [ishalfop](../functions/ishalfop.md), [key](../functions/key.md), [mychannels](../functions/mychannels.md), [nochops](../functions/nochops.md), [numonchannel](../functions/numonchannel.md), [onchannel](../functions/onchannel.md), [userhost](../functions/userhost.md).

### Display operations

Inspect windows, input, status lines, and retained output.

[chanwin](../functions/chanwin.md), [curpos](../functions/curpos.md), [currchans](../functions/currchans.md), [geom](../functions/geom.md), [getcap](../functions/getcap.md), [inputctl](../functions/inputctl.md), [isdisplaying](../functions/isdisplaying.md), [lastlog](../functions/lastlog.md), [levelwindow](../functions/levelwindow.md), [line](../functions/line.md), [notifywindows](../functions/notifywindows.md), [outputinfo](../functions/outputinfo.md), [querywin](../functions/querywin.md), [status](../functions/status.md), [status_oneoff](../functions/status_oneoff.md), [winchan](../functions/winchan.md), [windowctl](../functions/windowctl.md).

### Array operations

Use the separate built-in indexed-array facility; see [arrays](arrays.md) for item numbers versus sorted indices.

[delarray](../functions/delarray.md), [delitem](../functions/delitem.md), [delitems](../functions/delitems.md), [finditem](../functions/finditem.md), [finditems](../functions/finditems.md), [getarrays](../functions/getarrays.md), [getitem](../functions/getitem.md), [getmatches](../functions/getmatches.md), [getrmatches](../functions/getrmatches.md), [gettmatch](../functions/gettmatch.md), [ifindfirst](../functions/ifindfirst.md), [ifinditem](../functions/ifinditem.md), [ifinditems](../functions/ifinditems.md), [igetitem](../functions/igetitem.md), [igetmatches](../functions/igetmatches.md), [igetrmatches](../functions/igetrmatches.md), [indextoitem](../functions/indextoitem.md), [itemtoindex](../functions/itemtoindex.md), [listarray](../functions/listarray.md), [matchitem](../functions/matchitem.md), [numarrays](../functions/numarrays.md), [numitems](../functions/numitems.md), [rmatchitem](../functions/rmatchitem.md), [setitem](../functions/setitem.md), [usetitem](../functions/usetitem.md).

### File I/O

Open, read, write, seek, and inspect files through client-managed handles; open modes and failure values are function-specific.

[close](../functions/close.md), [dbmctl](../functions/dbmctl.md), [eof](../functions/eof.md), [ferror](../functions/ferror.md), [frewind](../functions/frewind.md), [fseek](../functions/fseek.md), [fsize](../functions/fsize.md), [fskip](../functions/fskip.md), [ftell](../functions/ftell.md), [ftime](../functions/ftime.md), [ftruncate](../functions/ftruncate.md), [isfilevalid](../functions/isfilevalid.md), [open](../functions/open.md), [randread](../functions/randread.md), [read](../functions/read.md), [write](../functions/write.md), [writeb](../functions/writeb.md).

### Client operations

Inspect and control aliases, keybindings, hooks, timers, settings, output levels, logs, encoding, and CTCP handlers.

[aliasctl](../functions/aliasctl.md), [bindctl](../functions/bindctl.md), [builtin_expando](../functions/builtin_expando.md), [cexist](../functions/cexist.md), [check_code](../functions/check_code.md), [ctcpctl](../functions/ctcpctl.md), [curcmd](../functions/curcmd.md), [encodingctl](../functions/encodingctl.md), [epic](../functions/epic.md), [execctl](../functions/execctl.md), [fix_arglist](../functions/fix_arglist.md), [fnexist](../functions/fnexist.md), [functioncall](../functions/functioncall.md), [getcommands](../functions/getcommands.md), [getfunctions](../functions/getfunctions.md), [getserial](../functions/getserial.md), [getset](../functions/getset.md), [getsets](../functions/getsets.md), [hookctl](../functions/hookctl.md), [iconvctl](../functions/iconvctl.md), [idle](../functions/idle.md), [info](../functions/info.md), [irclib](../functions/irclib.md), [levelctl](../functions/levelctl.md), [loadinfo](../functions/loadinfo.md), [logctl](../functions/logctl.md), [sequence_point](../functions/sequence_point.md), [startupfile](../functions/startupfile.md), [symbolctl](../functions/symbolctl.md), [tags](../functions/tags.md), [timerctl](../functions/timerctl.md), [xdebug](../functions/xdebug.md).

### Network operations

Address conversion and name resolution; availability and returned addresses depend on the host network environment.

[convert](../functions/convert.md), [iptolong](../functions/iptolong.md), [iptoname](../functions/iptoname.md), [longtoip](../functions/longtoip.md), [nametoip](../functions/nametoip.md).

### System operations

Filesystem and process operations, process environment, identity, and platform security interfaces.

[chmod](../functions/chmod.md), [exec](../functions/exec.md), [fexist](../functions/fexist.md), [getenv](../functions/getenv.md), [getgid](../functions/getgid.md), [getlogin](../functions/getlogin.md), [getpgrp](../functions/getpgrp.md), [getuid](../functions/getuid.md), [killpid](../functions/killpid.md), [mkdir](../functions/mkdir.md), [pid](../functions/pid.md), [pledge](../functions/pledge.md), [ppid](../functions/ppid.md), [realpath](../functions/realpath.md), [rename](../functions/rename.md), [rmdir](../functions/rmdir.md), [stat](../functions/stat.md), [ttyname](../functions/ttyname.md), [twiddle](../functions/twiddle.md), [umask](../functions/umask.md), [uname](../functions/uname.md), [unlink](../functions/unlink.md), [unveil](../functions/unveil.md).

### Miscellanea

Structured data, cryptographic helpers, identifiers, and list generation.

[ecdsatool](../functions/ecdsatool.md), [hash_32bit](../functions/hash_32bit.md), [jot](../functions/jot.md), [json_error](../functions/json_error.md), [json_explode](../functions/json_explode.md), [json_implode](../functions/json_implode.md), [jsontest](../functions/jsontest.md), [pbkdf2](../functions/pbkdf2.md), [scrambox](../functions/scrambox.md), [uuid4](../functions/uuid4.md).

## Script-provided functions

Loading [builtins](../scripts/builtins.md) supplies compatibility helpers including `b64encode`, `b64decode`, `encode`, `decode`, `urlencode`, `urldecode`, and `sha256`, implemented through the C `xform` interface. They are script aliases even though the package is named `builtins`. Some helpers overlap names still present in C; ordinary-call dispatch continues to prefer the C function.

Other examples are [notify](../scripts/notify.md), [scan](../scripts/scan.md), and [pipe](../scripts/pipe.md). These require their scripts to be loaded, and their effects differ: `scan` formats channel information, while `pipe` captures command output. A script helper can itself rely on other scripts or on removed interfaces, so use its current documentation rather than assuming that every installed script is operational.

## External scripting languages and availability

[python](../functions/python.md) and [pydirect](../functions/pydirect.md) are registered when EPIC is built with Python support. Their availability and argument rules are separate from EPIC alias functions. Platform-dependent functions, database support, regular-expression backends, security calls, and individual `xform` transformations also have their own build or platform requirements.

`fnexist(name)` tests for a registered C built-in, not a script alias. `getfunctions()` lists built-in function names; `aliasctl` and `symbolctl` provide script-symbol inspection. Registration alone is not a guarantee of useful behavior for every argument or build: consult the function's page and check its result. Server-related operations are also subject to connection state and network policy.

## Source references

- `source/functions.c`: `built_in_functions[]`, conditional registrations, `call_function()`, and individual handlers.
- `source/alias.c`: built-in/script symbol lookup and introspection.
- `source/commands.c`: alias function calls and return values.
- `source/expr.c` and `source/expr2.c`: text and expression call syntax.
- `script/builtins` and the linked scripts: script-provided compatibility and utility functions.
