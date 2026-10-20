# Etiquette on IRC

Good manners help keep a channel useful and welcoming. Channel expectations vary: read the topic and any published rules, observe the conversation, and follow the moderators' guidance. EPIC can send a message or automate an action without deciding whether that action is welcome.

## 1. Language

Use a language understood and welcomed in the channel. English is common in many channels, but it is not the only language used on IRC. Some communities prefer one language or a particular set of languages, often indicated by their topic or rules. Ask briefly if you are unsure rather than assuming everyone shares your preference.

To request the topic of your current channel, use:

```text
/topic
```

The server supplies the topic; it is not a client-generated statement of universal IRC rules.

## 2. Hello and goodbye

It is not necessary to greet every person who joins. On a busy channel, repeated greetings and departures can crowd out the discussion. A brief greeting is often enough. A private [msg](../commands/msg.md) can be more appropriate for a personal conversation, but unsolicited private messages are not automatically welcome either.

Automated greetings deserve particular care. EPIC's [on](../commands/on.md) hooks can react to joins and other events, but a greeting bot can be noisy, impersonal, or contrary to channel policy. Obtain the community's agreement before adding routine automated output, and avoid scripts that automatically respond to every message or arrival.

## 3. Channel discussion

A channel's name suggests its subject but does not explain every convention. Watch the discussion for a while and check the topic before posting. Join in when appropriate, keep questions relevant, and avoid repeatedly steering a conversation away from its purpose.

Give people time to answer. Repeatedly posting the same question, highlighting many nicknames, or sending a large block of text can be disruptive. For long examples or logs, ask how the channel prefers to receive them. A client's pacing script does not override the channel's rules or the server's flood limits.

## 4. Names, characters, and encodings

Do not assume unfamiliar punctuation in someone's nickname or message is a mistake. Historically, limited national character sets used characters such as braces, brackets, and vertical bars for letters, and some spellings persisted. There is no universal substitution table that you can safely apply to every person's name.

EPIC6 handles UTF-8 text and provides [encoding](../commands/encoding.md) controls for other encodings. Display still depends on the sender, terminal, configured conversion, and font. If text is garbled, ask about encoding before correcting someone's spelling. Server nickname rules and case comparison can also differ from ordinary text handling.

## 5. Think before sending

People form impressions from what you write and do. Before submitting a message, check the current channel or query target and whether the text belongs in a public conversation. A visible local echo does not give you a way to retract a message already sent to the network.

Disagreement need not become personal. Respect requests to stop, avoid unsolicited repeated contact, and use the community's moderation process when needed. Changing client software does not exempt anyone from network or channel policy.

## Credits

Original authors: Lea Viljanen (LadyBug) and Ari Husa (luru). Later contributors: Troy Rollo (Troy) and James Sneeringer (CrowMan).

## Client references

- [source/commands.c](../../source/commands.c): topic requests, message sending, target selection, and outbound recoding.
- [source/parse.c](../../source/parse.c) and [source/hook.c](../../source/hook.c): join events and programmable handlers.
- [source/recode.c](../../source/recode.c): UTF-8 and character-encoding conversion.

These references describe client mechanisms, not enforcement of the social guidance above. Etiquette itself is not a command, setting, or feature with a return value.
