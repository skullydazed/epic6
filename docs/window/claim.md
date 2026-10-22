# window claim

## Synopsis

```text
/window [<window>] claim <channel>
```

## Description

Claim a channel for the selected window during the [channel_claim](../on/channel_claim.md) hook. With no selector, use the current window. The channel must be currently claimable and on the selected window's server. The name is matched case-insensitively.

The channel is claimable only while that hook runs during channel setup. After the hook, EPIC assigns it to the last window that successfully claimed it, overriding the proposed window. This operation neither sends a join request nor moves an established channel; use [channel](channel.md) for those tasks.

Supply one channel name. A failed claim reports that the window could not successfully claim the channel. `/window -claim` does nothing.

## Examples

Assign claimable channels to window 3 when it is on the matching server:

```text
/on channel_claim "*" { /window 3 claim $1; }
```

## Source references

`windowcmd_claim` in [source/window.c](../../source/window.c); `add_channel` and `window_claims_channel` in [source/names.c](../../source/names.c).
