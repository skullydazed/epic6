# nothing

## Synopsis

```text
/bind <key> nothing
```

## Description

Remove the action assigned to the specified key or key sequence. For an ordinary single key, this makes it do nothing when pressed. `nothing` remains supported as a special value of [bind](../commands/bind.md); it is not a registered executable binding action.

Unbinding a sequence does not remove longer sequences that begin with it. For example, unbinding Escape alone does not remove Escape-b. An unbound sequence can also fall back to shorter bindings when EPIC resolves the input, so removing a sequence's binding does not guarantee that every key in it will be discarded.

The special value takes no options or additional arguments; extra text is ignored. It changes only the binding in the current keymap and does not send an IRC request.

## Limitations

Key sequences containing bytes with the high bit set cannot be bound or unbound this way; EPIC reports `Cannot bind sequences containing high bit chars`. Input code points above 127, and characters entered using [quote_character](quote_character.md), bypass binding lookup and insert themselves, so `nothing` does not disable them.

`/rbind nothing` reports `You cannot list all unbound keys.`

## Example

```text
/bind ^B nothing
```

This removes the usual Control-B action, so pressing Control-B no longer inserts a bold toggle. To restore that assignment:

```text
/bind ^B bold
```

See [bold](bold.md) and [bind](../commands/bind.md).

## Source references

- [source/keys.c](../../source/keys.c): `bind_compressed_string` recognizes `nothing` and clears the selected node's action; `key_exec` and `key_exec_bt` resolve unbound sequences; `handle_keypress` bypasses lookup for non-ASCII or quoted input; `rbindcmd` handles reverse lookup.
