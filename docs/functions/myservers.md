# $myservers()

## Synopsis

```text
$myservers()
$myservers(<anything>)
```

## Technical

With no input, returns a space-separated list of server names for records with an open connection descriptor. The name is EPIC's cached server-reported name when available, otherwise its configured host name. A connection still awaiting registration can be included. Port numbers, groups, and connection types are not included.

With any nonempty input, returns a space-separated list of server refnums for registered connections. The supplied text is ignored: `*`, `0`, a name, or any other text selects the same refnum-list behavior, not a particular server. Pending connections are excluded. Registration here means EPIC's server state is syncing or active.

Both forms examine the complete client server list, independently of the current window or execution server. Results follow ascending server refnum order. Closed records are omitted from the name form; unregistered records are omitted from the refnum form. The function does not connect, disconnect, or send an IRC request.

## Practical

The refnum form is useful when a script needs to iterate over registered connections. Names alone do not uniquely identify a connection: different records can share a host name while using different ports or other settings, and duplicate names are not removed.

Use [$serverctl()](serverctl.md) for details about an individual server. For setting away status on every server, [/away -all](../commands/away.md) performs the appropriate local state updates as well as sending requests. A loop of raw [/xquote](../commands/xquote.md) `AWAY` requests bypasses those local updates.

## Returns

Without input: the list of open server names. With nonempty input: the list of registered server refnums. Returns the empty string when no records satisfy the selected form. No status prefixes or quoting are added.

## Examples

```text
$myservers()       might return irc.example.net irc.other.net
$myservers(*)      might return 0 2
$myservers(0)      returns the same refnum list as $myservers(*)
```

To report every registered server reference without changing its state:

```irc
fe ($myservers(*)) server_ref {
    echo Registered server: $server_ref
}
```

## History

The no-argument form first appeared in ircII 2.2.2. The argument form first appeared in EPIC4pre1.028 and was suggested by Colten Edwards.

Registration and `function_servers()`: [source/functions.c](../../source/functions.c). Open-connection enumeration, server-name fallback, and registered-state filtering: [source/server.c](../../source/server.c), `server_list_to_string()`, `get_server_itsname()`, and `is_server_registered()`.
