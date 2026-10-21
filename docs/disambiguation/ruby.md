# Ruby: removed integration

The former Ruby topics described the embedded `ruby` command and `ruby()` function. EPIC6 has retired that integration: neither interface is registered in the current command/function tables, and configuration no longer provides an embedded Ruby interpreter. No bundled script replaces that bridge.

Old embedded-Ruby examples therefore have no current built-in execution or return-value contract. Installing Ruby on the system does not restore them. A user-defined alias with the same name supplies its own behavior rather than reactivating the former integration.

To run a separate Ruby program, use the [exec command](../commands/exec.md) with a locally installed interpreter, for example:

```text
/exec -direct ruby -e "puts 123"
```

The program runs as a local subprocess with pipes, not inside EPIC's interpreter. It does not share client aliases, local variables, or hooks. Consult exec for input, output callbacks, quoting, and cleanup. For native client scripting, see [programming](../special/programming.md).

## Source references

- `KNOWNBUGS`: explicit sunset of Ruby support.
- `UPDATES`: removed-feature list includes Ruby.
- `source/commands.c` and `source/functions.c`: current interface registries.
- `configure.ac`: current build integrations.
- `source/exec.c`: external-program alternative.
