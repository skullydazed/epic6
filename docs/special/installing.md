# Installing EPIC6

## Configure

Unpack a source archive or obtain a [source checkout](getting_epic.md), then change into its top-level directory. EPIC6 targets modern POSIX systems and requires a suitable C compiler and libraries; the original claim that it should build on almost any Unix was broader than the current configuration requirements. See [compile options](compile_opts.md) and the checks in `configure.ac`.

Run the supplied configuration script:

```sh
./configure
```

Its default installation prefix is `/usr/local`. To install in a directory you can write without system-wide privileges, choose a prefix explicitly, for example:

```sh
./configure --prefix="$HOME/.local"
```

Use `./configure --help` to inspect available options. If a dependency check fails, address the reported requirement before building. The prefix selects installation paths; it does not install missing build dependencies.

## Build

From the same top-level directory:

```sh
make
```

The current build creates `source/epic6` and the window helper `source/wserv4`, correcting the original EPIC4/EPIC5 executable names. Build success depends on the configured compiler, libraries, and platform.

## Install

After a successful build:

```sh
make install
```

This writes into the configured installation paths, so those directories must be writable by the installing user. The current targets install:

- The EPIC6 executable under the configured binary directory. The default `std` install type installs a commit-numbered executable and creates an `epic6` link; the `prod` type installs the executable under its configured name directly.
- The `epic6-wserv4` helper under the configured libexec directory.
- Supplied scripts under the configured data directory's `epic6/script` directory.
- The `epic6` manual page in `man1`.

Program-name transformation and directory options can change those names and locations. Contrary to the original page, the current `make install` targets do not install the Markdown help collection or an old help-file tree.

## Scripts and customization

A script pack is optional. EPIC's own scripting language and supplied scripts can customize display, input, and automation, and different packs offer different workflows. Choose scripts that support your EPIC6 version and check their dependencies; an older pack may call removed interfaces. Start with [the supplied startup scripts](../scripts/global.md) or [the scripting introduction](epic_scripting.md), and customize according to your needs rather than treating a script pack as an installation requirement.

## Source references

- [configure.ac](../../configure.ac) and [configure](../../configure): platform/library checks, options, executable naming, helper configuration, install type, and default prefix.
- [Makefile.in](../../Makefile.in): top-level build and installation targets, scripts, executable/link behavior, helper, and manual page.
- [source/Makefile.in](../../source/Makefile.in): EPIC6 and helper build targets.
