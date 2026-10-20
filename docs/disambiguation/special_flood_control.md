# Flood control (removed incoming detector)

The former built-in flood-control facility limited repeated incoming messages reaching the display. EPIC6 has removed that detector, its tracking list, and its configuration settings. It does not automatically suppress incoming messages through the former flood-control policy.

## How people were tracked

The former `flood_maskuser` setting grouped senders by user and host, distinguishing individual users or combining users on a host. Floods were tracked separately by target, server, and message level. These grouping rules are historical; EPIC6 does not maintain that incoming-flood table.

## The flood list

`flood_users` formerly limited the number of potential floods tracked. New senders displaced older entries, while penalty points could keep troublesome senders on the list longer. The former `floodinfo()` function inspected this state. EPIC6 supplies neither this list nor that built-in function.

## What was a flood?

`flood_after` selected how many incoming messages were needed before checking their rate against `flood_rate` and `flood_rate_per`. Messages above the threshold accumulated penalty points. These settings no longer configure a built-in detector, and EPIC6 provides no replacement defaults under those names.

## When you are flooded

The former `flood_warning` and `flood_ignore` settings controlled warnings and suppression. They are unavailable, and the former [on flood](on_flood.md) event is no longer generated. Obsolete assignments such as `/set flood_after 5` report an unknown setting unless a loaded script supplies its own handling. A script accepting one of these names implements its own policy; it does not restore the removed C detector.

Scripts can implement incoming-message counting through events such as [general_privmsg](../on/general_privmsg.md) and [general_notice](../on/general_notice.md). Suppressing local display does not stop the sender's traffic from reaching the connection or consuming bandwidth and processing time. Server-side flood policies are independent and depend on the IRC network.

## Other meanings of flood protection

The bundled [floodprot](../scripts/floodprot.md) script queues **outgoing** IRC lines. It is a separate facility and does not classify incoming senders or recreate the old flood list. To use it in a configuration that does not already provide its dependencies:

```irc
/load builtins
/load floodprot
```

The CTCP sender also retains an outbound reply-throttling branch. It is distinct from incoming-message detection and references the old `no_ctcp_flood` variable identifier, whose setting is no longer registered. Its timestamp is updated in the reply-drop branch, not after each successful reply, so its presence does not establish a reliable general reply-rate limit. See [no_ctcp_flood](set_no_ctcp_flood.md) for the unsupported setting interface. Neither this branch nor the script guarantees that an IRC server will accept outgoing traffic without rate-limit penalties.

## Source references

- `source/parse.c`: incoming message and notice processing uses current hooks without the former flood detector.
- `source/vars.c`, `source/functions.c`, and `source/hook.c`: current registration tables omit the former settings, `floodinfo`, and `flood` event.
- `KNOWNBUGS`: records retirement of the `flood` hook.
- `script/floodprot`: independent outgoing queue, conditional send hook, and repeating timer.
- `source/ctcp.c` and `include/vars.h`: retained outbound CTCP branch and old variable identifier.
