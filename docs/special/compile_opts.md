# Compile-time options

EPIC builds can include optional integrations. Scripts can inspect the running client's compact option string with `$info(o)`; the selector is the letter `o`, not zero. The returned text is a sequence of feature letters, without separators.

```text
/echo $info(o)
```

This describes the executable that is running, not the features available on an IRC server or the scripts currently loaded. It is a read-only query with no file access, server request, or state change.

## Current feature letters

The current implementation constructs the string in this order:

| Letter | Meaning | Build condition |
| --- | --- | --- |
| `h` | Implied `on` hooks are supported, as used by facilities such as [loadformats](../scripts/loadformats.md). | Always included. |
| `p` | Build configured with PCRE2 integration. | `HAVE_PCRE2` defined. |
| `r` | Build configured with libarchive support for archive-aware file loading. | `HAVE_LIBARCHIVE` defined. |

Possible strings in this source tree are `h`, `hp`, `hr`, and `hpr`. There is no universal default string independent of the build's dependency checks. The optional letters depend on configuration and detected libraries; they cannot be enabled by changing a runtime setting.

Do not use the old `2cefgnxz` string or its historical letter meanings as the expected EPIC6 result. This interface now reports only the entries above. An omitted legacy letter does not mean that local variables, color, `exec`, globbing, or another unlisted feature is unavailable; nor does it establish that a retired feature exists. Check the specific command/function and its current implementation.

For example, Python support is conditional on the build but has no letter in this option string. The string also does not identify a dependency's version, prove that a script's helper definitions have been loaded, or enumerate active runtime settings.

## Testing a reported option

Use a substring test rather than comparing the whole string, so optional or future letters do not break the check:

```text
if (index(p $info(o)) >= 0) {
    echo This build reports PCRE2 support.;
};
```

This tests the report; it does not compile or validate a regular expression. The option string itself has no error or success code: `$info(o)` returns text. The broader `info` function uses other selectors for version and build information.

## Build details

For more detail, `$info(z:configure_args)`, `$info(z:compiler_version)`, `$info(z:cflags)`, `$info(z:libs)`, and `$info(z:final_link)` expose recorded build strings. They are informational strings, not a replacement for feature-specific checks or values to execute as commands. The configuration source and generated definitions establish which optional integrations were selected.

## Source references

- [include/options.h](../../include/options.h): the authoritative `compile_time_options` string and its three current letters.
- [source/functions.c](../../source/functions.c): `function_info` returns the option string for selector `o` and exposes build-information selectors.
- [configure.ac](../../configure.ac) and [include/defs.h.in](../../include/defs.h.in): optional dependency checks and generated feature definitions.
- [include/irc_std.h](../../include/irc_std.h): PCRE2 header integration.
- [source/elf.c](../../source/elf.c): libarchive-enabled file loading.
- [source/hook.c](../../source/hook.c): implied-hook implementation.
