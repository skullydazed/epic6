# tabkey.jm script

## Synopsis

```text
/load tabkey.jm
/set nick_completion_char <text>
/set static_nick_complete_list <nicknames...>
/set tab_history_cycle_size <number>
/set zsh_style_completion on|off
/tclear
```

## Description

This script implements context-sensitive Tab completion for the input line, including command and alias names, channel names, nicknames, settings, file paths, executable names, and script names. It also keeps a message-target history for `msg` and `notice` input.

Loading binds Tab (`^I`) to forward completion/cycling and Control-R (`^R`) to reverse cycling. It replaces any existing assignments without saving them. Completion edits the input buffer; it does not submit the edited line or execute a completed command.

The script works on the fragment before the cursor, including fragments inside quoted text, so completion can be attempted in the middle of a line. A unique completion is inserted, with context-dependent spacing or a configured nickname suffix. For ambiguous results it can extend the common prefix or print possible matches. With `zsh_style_completion on`, another press on unchanged input cycles through the saved possibilities; Control-R cycles backward. Control-R does not initiate an ordinary fresh completion.

## Completion contexts

| Input context | Candidate source |
| --- | --- |
| First command word beginning with `/` | Built-in commands and user aliases. |
| First argument after `/set`, including a leading `-` | Registered setting names plus this script's four pseudo-settings. |
| First command word after `/exec` | Executable candidates from `PATH`, plus relevant local directories or explicit paths. |
| Later `/exec` arguments | Local file glob matches. |
| `/load` or `/unload` arguments | Files from `load_path` and the current directory; explicit paths use local glob matching. This is file completion, not a list of loaded packages. |
| Other text | Joined channel names and nickname candidates. |

Nickname candidates start with users cached on the current channel plus `static_nick_complete_list`. Only when those produce no matches does the script search other joined channels. The old notify-list fallback now contributes nothing with the supplied [notify](notify.md) implementation, whose function branch returns empty. Matching uses client wildcard patterns, not escaped literal-prefix comparisons in every context.

The retained `/help` branch calls `match_help`, but that helper's definition is commented out, so help-topic completion is unavailable unless another script supplies it. Historical completion handlers do not establish that the command or feature they name is supported by the current client.

File matching uses filesystem globbing and tries to quote inserted names containing spaces. It is not a general shell parser: nested or escaped quotes, unusual wildcard characters, cursor positions inside complex commands, and non-ASCII text can expose its mixture of byte lengths and character indices. Executable filtering checks mode bits through `stat`, not whether the current user can actually execute a candidate under every permission mechanism. Completing a command name does not validate the command.

## Configuration

The script intercepts [set](../on/set.md) to implement these pseudo-settings as global variables; they are not registered setting objects. Queries print its own value messages. Loading reapplies all defaults and clears completion/history state.

| Name | Default | Meaning |
| --- | --- | --- |
| `nick_completion_char` | Empty | Suffix appended to a unique nickname completion when it is the line's first word. Use `set -nick_completion_char` to clear it. |
| `static_nick_complete_list` | Empty | Additional nickname candidates even when absent from the current channel. Clear with `set -static_nick_complete_list`. |
| `tab_history_cycle_size` | `10` | Maximum target-history size; use a positive integer. The script rejects nonnumeric values and values below 1, and trims existing history when changed. |
| `zsh_style_completion` | `off` | Enable repeated-match cycling. Accepts `on` or `off`; it does **not** implement `toggle`. |

There is no enable/disable setting for the entire script. It also turns `xdebug extractw` on globally and does not restore the previous state. Its many generic aliases, including `isdirectory`, `isexe`, and `match_file`, can replace other definitions.

## Target history

Press Tab on an empty input line to start cycling remembered `msg`/`notice` command-target pairs; Control-R cycles in the opposite direction. The script replaces the whole input line with the selected command and target. It also cycles when it recognizes a two-word `/msg` or `/notice` line ending in a space. `/tclear` empties this target history and prints a confirmation; it does not clear all completion state.

Targets are recorded from serial-110 incoming and outgoing message/action/notice hooks, with duplicate command-target pairs moved to the recent end. If more than one server is connected, ordinary targets are stored with a `-refnum/target` selector. Current built-in message sending recognizes that selector, but saved history depends on server-refnum stability.

The incoming `general_notice` handler records `$1`, which is the destination rather than the sender under the current hook layout. It can therefore add your own nickname or a channel as a notice-history target. The script does not filter all channel targets from every history hook. Treat suggested targets as suggestions and inspect them before sending.

The ordinary completion path combines channel and nickname matches, while those methods separately replace the saved cycling list. Consequently the cycling list can omit channel candidates that appeared in the combined display. History and completion state are shared across windows; this is not isolated per-input-window state.

## Requirements and limits

The `encode`, `servernum`, and `myservers` helpers from [builtins](builtins.md) must already be loaded. No additional helper script or external completion program is loaded. Filesystem completion needs access to the paths being scanned. Core nickname completion uses local cached membership, so results can be incomplete while channels synchronize.

It also installs serial-zero conditional `send_to_server` hooks that try to reinterpret colon-bearing nonchannel `privmsg` and `notice` targets as server/target selectors. These hooks inspect outgoing traffic beyond completion itself and can conflict with another routing script. Other completion scripts can replace these bindings and hooks. The script has no package declaration, automatic restoration, or single removal command. Completion itself normally sends no IRC request; sending the resulting line later uses the selected command and server rules. Helper aliases return candidate strings internally, but there is no stable public function-return contract for the key action.

## Example

```text
/load tabkey.jm
/set nick_completion_char :
/set static_nick_complete_list example example_alt
/set zsh_style_completion on
```

Type part of a nickname or `/set` name and press Tab. Use Control-R to reverse an established cycle, and review the edited text before pressing Enter. Use `/tclear` if remembered message targets are no longer appropriate.

## History

This script was written by nsx (jm) and first appeared in EPIC4-1.1.13. Its numbered hooks use serial 110.

## Source references

- [script/tabkey.jm](../../script/tabkey.jm): key actions, contexts, pseudo-settings, history, quoting, and compatibility limits.
- [script/builtins](../../script/builtins): helper dependencies.
- [script/notify](../../script/notify): empty notify function branch.
- [source/functions.c](../../source/functions.c): glob, stat, command/setting enumeration, and common-prefix helpers.
- [source/input.c](../../source/input.c) and [source/keys.c](../../source/keys.c): cursor editing, reset-line behavior, and bindings.
- [source/commands.c](../../source/commands.c): `send_text` handles `-server/target` selectors.
- [source/parse.c](../../source/parse.c): `general_notice` supplies sender, destination, and text.
