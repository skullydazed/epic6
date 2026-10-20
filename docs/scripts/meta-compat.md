# meta-compat

## Synopsis

```text
/load meta-compat
/bind <sequence> meta<number>_character
/bind meta<number>-<suffix> <action> [<arguments>]
```

## Description

Older ircII scripts used numbered meta states to describe multi-key bindings. EPIC6 binds arbitrary key sequences directly, so its C binding actions no longer implement those old meta-state commands. `meta-compat` translates the old notation into ordinary sequence bindings for legacy scripts.

Load it before the old script. It defines a replacement `bind` alias, records which sequences introduce each meta level, and expands `meta<number>-<suffix>` for every known prefix of that level. A legacy assignment can be remembered before its prefix is defined; assigning the prefix later replays the stored suffix bindings. It also accepts `meta<number>` without `_character` as the prefix action shorthand.

Translation uses `encode` and `decode` from [builtins](builtins.md), normally supplied by default startup. The resulting action must still be registered with EPIC. This script translates sequence notation; it does not recreate every retired action named by an old configuration.

## Default prefixes

On load, the script initializes these compatibility definitions:

| Meta level | Prefixes |
| --- | --- |
| `meta1` | Escape (`^[`). |
| `meta2` | Control-X (`^X`), Escape-O (`^[O`), and Escape-[ (`^[[`). |
| `meta32` | Any `meta2` prefix followed by `1`. |
| `meta33` | Any `meta2` prefix followed by `4`. |
| `meta30` | Any `meta2` prefix followed by `5`. |
| `meta31` | Any `meta2` prefix followed by `6`. |

These are its EPIC4-1.0.1 compatibility defaults, not a complete statement of your terminal's key sequences. Ordinary sequence characters remain case-sensitive under the built-in binder, while action names and script meta identifiers use the client's usual case-insensitive name handling.

## Binding changes and reset

Translated assignments eventually call the C [bind](../commands/bind.md) command. Non-meta assignments and queries are normally forwarded too. Replacing a sequence that previously introduced a meta level removes tracked dependent bindings for that prefix. Binding a legacy suffix to `nothing` removes its stored action and unbinds the translated sequences.

`/bind -defaults` is specially intercepted: it runs the C binding reset, clears compatibility metadata, and reinstalls the prefix definitions above. That C reset also discards custom registered binding actions. Merely loading or rerunning `metacompat.defaults` resets compatibility metadata without performing that full C reset; existing translated bindings are not comprehensively undone.

Use `//bind` to bypass this alias for a direct built-in operation. Such changes are not tracked by the compatibility layer and can leave its metadata inconsistent with the actual bindings. Legacy meta queries are not a complete read-only emulation either: the meta branch writes its remembered action from the supplied action text, so querying without an action can clear that remembered definition. Prefer direct built-in inspection of the actual expanded sequence.

## Limitations

The sticky behavior of historical META4 is not supported; it behaves like an ordinary meta level. Recursive or cyclic prefix definitions can trigger `meta-compat: recursed too many times for /bind ...` after its depth counter exceeds 100. That early return does not unwind the counter normally, so a bad definition can interfere with later translations rather than being a clean rejected transaction.

Loading or resetting the layer changes binding behavior and metadata immediately. It has no script-specific settings, persistence file, IRC requests, or function return value. Other scripts can replace its `bind` alias; it is not an independent parser installed inside the C binder.

For new configuration, write explicit key sequences rather than adding meta states. For example, an Escape-x binding can be written directly as `/bind ^[x forward_word` without this translator.

## Example

```text
/load meta-compat
/bind ^[ meta1_character
/bind meta1-x forward_word
```

This associates Escape with meta level 1 and binds Escape followed by lowercase x to `forward_word`. It changes local input behavior only.

## History

Written by Ben Winslow (|Rain|); first included in EPIC4-1.2.5. Arbitrary sequence binding had already replaced the need for manual meta-state control in EPIC4pre2.001.

## Source references

- [script/meta-compat](../../script/meta-compat): replacement alias, remembered prefixes/suffixes, defaults, and recursion guard.
- [source/keys.c](../../source/keys.c): direct sequence parsing, action validation, `nothing`, and default reset.
- [script/builtins](../../script/builtins): encoding/decoding wrappers used for metadata keys.
