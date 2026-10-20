# Copyright information about EPIC

EPIC6's repository-wide copyright and redistribution terms are recorded in [COPYRIGHT](../../COPYRIGHT). The text below reproduces those notices and terms with Markdown formatting. Retain any additional notices supplied with individual files or dependencies; this page is not an inventory of every component's license.

## Copyright notices

- Copyright (c) 1990 Michael Sandroff.
- Copyright (c) 1991, 1992 Troy Rollo.
- Copyright (c) 1992-1996 Matthew Green.
- Copyright 1994 Jake Khuon.
- Copyright 1993-2024 EPIC Software Labs.

All rights reserved.

## Redistribution terms

Redistribution and use in source and binary forms, with or without modification, are permitted provided that the following conditions are met:

1. Redistributions of source code must retain the above copyright notice, this list of conditions and the following disclaimer.
2. Redistributions in binary form must reproduce the above copyright notices, the above paragraph (the one permitting redistribution), this list of conditions and the following disclaimer in the documentation and/or other materials provided with the distribution.
3. The names of the author(s) may not be used to endorse or promote products derived from this software without specific prior written permission.

## Disclaimer

THIS SOFTWARE IS PROVIDED BY THE AUTHORS ``AS IS'' AND ANY EXPRESS OR IMPLIED WARRANTIES, INCLUDING, BUT NOT LIMITED TO, THE IMPLIED WARRANTIES OF MERCHANTABILITY AND FITNESS FOR A PARTICULAR PURPOSE ARE DISCLAIMED. IN NO EVENT SHALL THE AUTHORS BE LIABLE FOR ANY DIRECT, INDIRECT, INCIDENTAL, SPECIAL, EXEMPLARY, OR CONSEQUENTIAL DAMAGES (INCLUDING, BUT NOT LIMITED TO, PROCUREMENT OF SUBSTITUTE GOODS OR SERVICES; LOSS OF USE, DATA, OR PROFITS; OR BUSINESS INTERRUPTION) HOWEVER CAUSED AND ON ANY THEORY OF LIABILITY, WHETHER IN CONTRACT, STRICT LIABILITY, OR TORT (INCLUDING NEGLIGENCE OR OTHERWISE) ARISING IN ANY WAY OUT OF THE USE OF THIS SOFTWARE, EVEN IF ADVISED OF THE POSSIBILITY OF SUCH DAMAGE.

## Viewing the embedded text

The built-in [license](../commands/license.md) command displays the original license text embedded in the executable:

```text
/license
```

It works locally without a server connection and takes no required arguments. Its embedded text includes older copyright-year information and transcription differences; it does not read the current repository's `COPYRIGHT` file at runtime or collect notices from all linked libraries. Use the files supplied with the particular source or binary distribution for its notices and terms.

## Source references

- [COPYRIGHT](../../COPYRIGHT): notices and terms reproduced above.
- [source/commands.c](../../source/commands.c): `license` registration and embedded display text.
