# activity

## Synopsis

```text
/load activity
```

## Description

A script-provided alternative to the built-in notified-window list. It tracks activity severity and builds a status list containing window refnums and abbreviated channel or query names, using color and bold formatting to distinguish levels.

Place `%2` in a [status_format](../set/status_format.md) line to show its list. `%Y` is an equivalent field: both display `status_user2`, which this script overwrites or unsets as it refreshes activity. Normal user-field visibility rules apply, including [show_status_all](../set/show_status_all.md).

The script also configures activity format 1 for `%E`, displaying a window refnum with an abbreviated channel/query label, or the refnum alone. It initializes existing windows to activity 1 and installs a window-creation hook to do the same for new windows. These operations replace existing activity-format-1 configuration and selected activity values.

This script expects the compatibility functions supplied by `builtins`, including `winnum`, `winquery`, and `winrefs`. It is not loaded merely by adding `%2` or `%E` to a format.

## Activity levels

The script observes [on window](../on/window.md) and classifies the output context:

| Level | Output classification | List appearance |
| --- | --- | --- |
| `1` | Reset/normal state | Omitted from the activity list. |
| `2` | Other output levels, including joins or modes | IRC color 14. |
| `3` | `publics`, or `actions` with a channel target | Normal text. |
| `4` | `msgs`, or `actions` without a channel target | Bold text. |

For a window other than the script's selected input window, activity increases only when the new severity is greater. A reset to 1 is allowed regardless. The switch-window handler resets the newly selected input window to 1 and rebuilds the list. Its `inputwin` helper determines the input window from the highest window priority.

The list includes all windows with activity above 1, not only windows currently hidden. It is independent of the built-in notified flag, `notify_level`, and `%F`. Entries are sorted numerically by window refnum. A label prefers the window's current channel, then its query, and strips formatting from the abbreviated name. The list begins with `act.` and uses a reset-formatting control at the end, which can interact with reverse-video status designs.

## Settings

| Script setting | Default | Purpose |
| --- | --- | --- |
| `chanact_length` | `4` | Maximum label length used in `%2` list entries. |
| `winname_length` | `6` | Maximum label length used by the configured `%E` activity format. |

These integer settings accept nonnegative values through normal set validation. They limit the name portion, not the entire entry including refnum and separator. Changing them does not itself rebuild the list; later activity refreshes use the current value. The `%E` expression reads its value during activity-format expansion.

## Example

```text
/load activity
/set chanact_length 8
/set winname_length 12
/set status_format [%R] %N%C %2 %E 
```

This replaces the global single-row format with one including both indicators; per-window overrides or double-status formats can require corresponding changes. Preserve any desired existing fields when editing your own format.

## Limitations and customization

The list's formatting is defined in the script rather than through separate format settings. Loading it can conflict with another script using `status_user2`, activity format 1, or its hook serials. It does not create per-level activity formats 2–4 for `%E`; its activity list and current selected-window format are distinct mechanisms.

Custom scripts can call `_proc_act_increase <window-refnum> <level>` to use its update mechanism; levels 2–4 have list formatting defined. The window hook observes routed output, not raw IRC events, and other hooks or output suppression can affect what reaches it. Loading or updating the indicators sends no IRC request.

## Source references

- [script/activity](../../script/activity): settings, aliases, severity classification, list construction, and reset/creation hooks.
- [script/builtins](../../script/builtins): compatibility window functions used by the script.
- [source/window.c](../../source/window.c): activity-format/data selection and window priorities.
- [source/status.c](../../source/status.c): `%2`/`%Y` user-field mapping and `%E` activity-format expansion.
- [source/functions.c](../../source/functions.c): `outputinfo` supplies the current output level and target.
