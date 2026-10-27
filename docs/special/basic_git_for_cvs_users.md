# Keeping up with EPIC in Git

To obtain EPIC6, use `git clone`, the rough equivalent of `cvs co`:

```sh
git clone https://git.epicsol.org/epic6.git
cd epic6
```

The clone URL matches this EPIC6 repository's configured `origin`. The original document used these commands for the older EPIC versions:

```sh
git clone https://git.epicsol.org/epic5.git
git clone https://git.epicsol.org/epic4.git
```

To update, run this inside your checkout:

```sh
git pull
```

This is the closest counterpart to `cvs update`, but Git fetches and integrates changes into the current branch according to its upstream and pull configuration. Local changes or divergent history can require additional action; it is not a guarantee that every checkout can always update without intervention.

To produce a patch for unstaged changes to tracked files:

```sh
git diff --no-color
```

Git's normal patch format is already unified; `-u` is optional. Use `git diff --cached --no-color` for staged changes, or `git diff HEAD --no-color` for both staged and unstaged changes to tracked files. Untracked files do not appear in these diffs.

The original document stated that its maintainer did not intend to use branches. That historical intention is not a restriction on EPIC6 development or a promise about future branch use.

## Validation

The EPIC6 clone URL was checked against the local repository's `origin` configuration. The commands describe Git operations on the source checkout; they do not depend on EPIC's C implementation. Older clone URLs are retained as historical examples, without a claim that their remote services were tested.
