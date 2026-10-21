# Map

Do you mean:

- The built-in [map command](../commands/map.md), which forwards `MAP` and any arguments to the IRC server? The server must support the request and supplies its own network-topology output.
- The [map script](../scripts/map.md), loaded with `/load map`, which defines an alias that can build a local topology display from `LINKS` replies? With no argument and a nonempty advertised `MAP` token, the script instead requests the server's own map.

Loading the script changes what ordinary `/map` invokes. Its optional server argument selects a center from returned LINKS data; it is not a request to connect your client to that server. Permissions, hidden topology, available replies, and supported server commands depend on the network.

See the individual pages for syntax, rendering, dependencies, and limitations.

## Source references

- `source/commands.c`: built-in `map` registration and generic protocol sender.
- `script/map`: alias, advertised-feature branch, LINKS collection, and local rendering.
