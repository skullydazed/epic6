# EPIC scripts

EPIC is a programmable IRC client in the ircII tradition. Scripts can define commands and event handlers and provide a different, more complete user experience. The original documentation described the client by itself as a bare-bones ircII replacement and noted that users commonly pair it with a script pack.

Script packs differ because users want different experiences, and many offer customization. The links below preserve the original page's historical listings; they do not establish current maintenance, availability, or compatibility with EPIC6. Check a pack's own documentation before using it with this version.

## Scripts for EPIC5

- [Amnesiac](http://amnesiac.ircii.org/)
- [Darkstar](http://www.epicsol.org/~brian/darkstar/)
- [LiCe5](https://lice.muppetz.com/)

## Scripts for EPIC4

The original page reported that most, possibly all, EPIC4 script packs were no longer maintained or supported, and suggested searching for them. It also warned that a former third-party EPIC4 script repository had been compromised. Those are historical statements from the page, not a current audit of every EPIC4 script or download site.

## Current EPIC6 source references

The client registers [alias](../commands/alias.md), [on](../commands/on.md), and [load](../commands/load.md) in [source/commands.c](../../source/commands.c). These support script-defined commands, event handlers, and script loading. The bundled [script directory](../../script/) contains individual scripts; [script/global](../../script/global) illustrates combining them into a setup. A bundled script collection and an external script pack can have different installation instructions and requirements.
