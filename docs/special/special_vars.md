# Special variables and expandos

In addition to variables created with [assign](../commands/assign.md), local variables, and built-in [set](../commands/set.md) settings, EPIC provides dynamic expandos that read client state. Their values are obtained when expansion occurs; they are not IRC requests.

## Forms and modifiers

| Form | Meaning |
| --- | --- |
| `$variable` | Visible local variable, then global variable, built-in expando or setting, then environment; unresolved names produce an empty string. |
| `$[num]variable` | Pad or truncate to the requested width; negative widths right-align. Padding uses `pad_char`. |
| `$#variable` | Word count of the value; `$#` alone counts the current arguments. |
| `$@variable` | Byte length of the value; `$@` alone measures the current argument string. |
| `$(text)` | Expand the text, then use the result as an expando name. Nesting is supported. |
| `${expression}` | Substitute the expression's result, with an explicit boundary before surrounding text. |
| `$function(arguments)` | Substitute a built-in or script function's result. |
| `$"prompt"` | Prompt and wait for a complete input line. |
| `$'prompt'` | Prompt and wait for a key response. |
| `$$` | A literal dollar sign. |

`$@` counts UTF-8 bytes, not Unicode characters or terminal columns. EPIC names, including single-letter expandos, are case-insensitive. Environment lookup retains the requested spelling and can be case-sensitive. User variables can shadow built-in expandos; using descriptive names avoids accidental collisions. See [expando substitution](expando_substitution.md) for timing, scope qualifiers, quoting, and width details.

## Alias arguments

| Expando | Value |
| --- | --- |
| `$*` | Entire argument string supplied to this evaluation. |
| `$n` | Argument number `n`, starting at zero. |
| `$n-m` | Arguments `n` through `m`, inclusive. |
| `$n-` | Arguments from `n` onward. |
| `$-m` | Arguments through `m`. |
| `$~` | Last argument. |

Here `n` and `m` represent numbers, not the literal nickname and channel-mode expandos. Word extraction and quoted-word handling depend on the applicable parser settings. Pass arguments explicitly where needed:

```irc
alias greet { msg $0 Hi there! $1-; }
```

`/greet friend welcome back` sends `Hi there! welcome back` to `friend`.

## Dynamic client values

| Expando | Value |
| --- | --- |
| `$,` | Last received private-message nickname recorded for the server context. |
| `$.` | Last sent private-message nickname recorded for the server context. |
| `$:` | Last joined nickname recorded for the server context. |
| `$;` | Last public-message nickname recorded for the server context. |
| `$a` | Away message for the server context. |
| `$b` | Last sent private-message body recorded for the server context. |
| `$c` | Current window's channel, or `0` when absent. |
| `$e` | Seconds since the client's recorded idle-time timestamp. |
| `$f` | Client start time as epoch seconds, like the format used by `time()`. |
| `$g` | Banner text, expanded when `banner_expand` is enabled. |
| `$h` | Current numeric context, formatted with a minimum width of three digits. |
| `$i` | Last invited channel recorded for the server context. |
| `$j` | Client version string. |
| `$k` | First character of `cmdchars`, with the built-in fallback if unset. |
| `$l` | Current input-line contents. |
| `$m` | Cached modes of the current window's channel, or empty. |
| `$n` | Nickname on the current window's server, or empty without a server. |
| `$o` | `status_oper` text when the server context marks you as an IRC operator, otherwise empty. |
| `$p` | `@` when the cached current-channel state marks you as an operator, otherwise empty. |
| `$q` | Current window's query target, or empty. |
| `$r` | Cached server version for the server context, falling back to the primary server when there is no context. |
| `$s` | Reported server name for the server being parsed, falling back to the current window's server. |
| `$t` | Current window's send target: query first, otherwise channel, or empty. |
| `$u` | Client cut buffer. |
| `$v` | Internal client release/version identifier string. |
| `$w` | Working directory, or empty if it cannot be read. |
| `$x` | Cached userhost for the server context. |
| `$y` | Realname stored for the current window's server, or empty without a server. |
| `$z` | Client's formatted clock string; formatting settings can change it. |

Server-context values and current-window values can refer to different connections inside hooks and callbacks. Cached values reflect information already recorded, not a fresh server lookup. A value can be empty before the corresponding state has been learned or recorded. The punctuation expando `$:` stands alone; `$:name` is a scope-qualified variable lookup with different meaning.

## Indirection and embedding

```irc
assign foo blah
assign blah 10
eval echo $($foo)
```

The last command displays `10`: `$foo` supplies `blah`, which is then looked up. In a script body, ordinary command statements already receive an expansion pass; interactive input generally needs `eval` to request it.

Use braces to prevent adjoining text from becoming part of a variable name:

```irc
eval echo foo${n}foo
eval echo ${[foo]}bar
```

The first embeds the nickname; `$nfoo` would instead look up the name `nfoo`. The second displays `foobar`. See [expressions](expressions.md) and [expandos](expandos.md).

## Source references

- `source/functions.c`: built-in expando registry and value handlers.
- `source/alias.c`: variable lookup and shadowing.
- `source/expr.c`: argument expandos, prompts, indirection, and modifiers.
- `source/window.c`: window channel, query, and send target.
- `source/server.c`: cached per-server values.
