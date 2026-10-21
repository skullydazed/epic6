# Channel

Do you mean:

- The [channel command](../commands/channel.md), which displays locally known channel information or requests joining channels? [join](../commands/join.md) uses the same built-in handler. Admission and channel rules depend on the IRC server.
- The [channel function](../functions/channel.md), `$channel([channel-name])`, which returns a formatted nickname list from the client's cached membership information? It does not join a channel or request a fresh list.

For assigning or selecting a channel in a particular window, see [window channel](../window/channel.md). For how channels and queries determine where input is sent, see [window targets](../special/window_targets.md).

## Source references

- `source/commands.c`: `channel`/`join` command registration and shared handler.
- `source/functions.c` and `source/names.c`: `channel()` and cached-member scanning.
- `source/window.c`: window channel selection.
