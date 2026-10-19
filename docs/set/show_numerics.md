# set show_numerics

## Synopsis

```text
/set show_numerics [on|off|toggle]
```

Default: `off`. With no argument, [set](../commands/set.md) displays the current value.

## Description

When on, output using EPIC's numeric banner helper shows the current three-digit reply or error number instead of the configured [banner](banner.md), normally `***`. This can help identify server replies and choose numeric hooks for scripts. For example, an applicable reply prefix can become `001` or `401`.

The number is substituted only while the current numeric is positive. Otherwise the helper uses the configured banner, honoring [banner_expand](banner_expand.md) as usual. When the numeric is substituted, the stored banner is not expanded for that prefix.

## Scope and limitations

This changes formatting, not which server replies arrive or are processed. It does not reveal suppressed replies, bypass hooks, or add a numeric prefix to every output line. Some replies have special formatting or named handlers; scripts can also replace their displays. Not every server message has a numeric code.

The helper is used by many built-in numeric displays and [xecho](../commands/xecho.md)'s `-b` option. A script calling `xecho -b` while handling a numeric can therefore receive that numeric prefix too. Plain script output or custom prefixes need not use it.

Changing this setting affects later formatting and does not rewrite existing scrollback or send an IRC request. [Show_end_of_msgs](show_end_of_msgs.md) is a separate script policy for selected completion markers; enabling numeric prefixes does not override it.

## Example

```text
/set show_numerics on
/whois alice
```

Applicable WHOIS reply displays use their numeric prefixes, subject to normal hooks and display handling. Restore the usual banner with `/set show_numerics off`.

## Source references

- [source/vars.c](../../source/vars.c) and [include/config.h](../../include/config.h): boolean registration and off default.
- [source/numbers.c](../../source/numbers.c): `banner` selects the numeric or configured banner; numeric processing sets and restores the current numeric and handles reply-specific displays.
- [source/commands.c](../../source/commands.c): `xecho -b` uses the banner helper.
