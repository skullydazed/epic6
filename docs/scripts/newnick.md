# newnick script

## Synopsis

```text
/load newnick
/set auto_new_nick on|off|toggle
/set auto_new_nick_char <character>
/set auto_new_nick_list <nicknames...>
/set auto_new_nick_length <number>
```

## Description

This script implements automatic nickname selection when the client needs a replacement during server registration. It provides the nickname-mangling facility previously built into the client.

The [new_nickname](../on/new_nickname.md) hook receives the rejected pending nickname. If `auto_new_nick` is on, the script chooses another candidate and submits [nick](../commands/nick.md) in that server's context. The IRC server decides whether to accept it; a later rejection can invoke the script again. Rejection of a nickname change after registration does not invoke this hook and is not handled by this script.

If a nickname list is configured, the script finds the rejected nickname in that list, ignoring case, and tries the following entry. If it is not in the list, it starts with the first entry. When there is no following entry, or the list is empty, it mangles the rejected nickname instead:

- Below the configured length, append `auto_new_nick_char`.
- At or above that length, prepend the nickname's final character to its first `length - 1` characters. For an ordinary nickname exactly that length, this moves its final character to the front; for a longer one it also discards intervening characters.

If `auto_new_nick` is off, the script opens a `Nickname: ` input prompt and submits the entered choice. Because input is asynchronous, the client may also print its normal instructions to use `/nick` before the answer is entered. The prompt callback uses the server context current when its `nick` command runs; unlike the automatic branch, it does not explicitly save and select the rejected server. Take care when switching connections while answering.

## Settings

These are script-provided settings created through [addset](addset.md), not built-in nickname policy settings.

| Setting | Default | Meaning |
| --- | --- | --- |
| `auto_new_nick` | `on` | Choose replacements automatically; `off` prompts instead. Supports `toggle`. |
| `auto_new_nick_char` | `_` | Character appended while below the configured length. The character setting accepts a single-column character. |
| `auto_new_nick_length` | `9` | Assumed nickname-length threshold, not a value negotiated with the server. Use a positive value appropriate for the network. |
| `auto_new_nick_list` | Empty when first created | Space-separated alternate nicknames, tried by their position relative to the rejected name. |

The first three defaults are explicitly reapplied when loading this script. The list has no explicit reset assignment. Settings are shared across connections, rather than stored separately for each network. The integer setting accepts zero, but the mangling algorithm is not designed to produce useful alternatives with that value.

## Limits and requirements

There is no guarantee of finding an available nickname. Repeated characters, duplicate list entries, reserved names, invalid characters, length restrictions, or server rate limits can cause repeated rejection. Rotation can cycle, and a mangled nickname absent from a nonempty list starts that list again on its next rejection. The script has no retry limit, delay, availability check, or recovery timer. It does not reclaim your preferred nickname later.

The script loads `addset` itself and is loaded by the supplied [global](global.md) startup script. A personal startup file that replaces that default startup may need to load it explicitly. No external program or optional platform feature is required. Other scripts replacing the same serial-zero hook can displace this behavior.

The internal `$newnick.mangle(nickname)` function returns a generated candidate using the current character and length settings; it sends no request itself. Normal automatic handling sends a `nick` request and has no script-specific return value or success announcement.

## Example

```text
/load newnick
/set auto_new_nick_list example example_ example_alt
/set auto_new_nick_length 12
```

If registration rejects `example`, this configuration tries `example_`, then `example_alt`. Rejection of the final entry leads to mangling; later rejections can return to the list. Configure these values before starting the connection for which they are needed.

## History

The automatic-new-nickname feature first appeared in EPIC2pre3. This script was written by BlackJac and first appeared in EPIC5-0.0.6; the retained source is dated May 16, 2005.

## Source references

- [script/newnick](../../script/newnick): selection, mangling, input prompt, and setting defaults.
- [script/addset](../../script/addset): creation of script-provided settings.
- [script/global](../../script/global): default startup load.
- [source/server.c](../../source/server.c): `nickname_change_rejected`, `reset_nickname`, and `change_server_nickname` distinguish registration and submit replacements.
- [source/numbers.c](../../source/numbers.c): server rejection numerics invoke nickname recovery.
- [source/functions.c](../../source/functions.c): `findw` finds the first case-insensitive list match; `right` and `mid` construct mangled names.
- [source/vars.c](../../source/vars.c): character and nonnegative-integer setting validation.
- [source/commands.c](../../source/commands.c) and [source/screen.c](../../source/screen.c): input prompt creation and deferred callback execution.
