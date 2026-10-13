# pbkdf2 function

## Synopsis

`$pbkdf2(<password text>)`

## Technical

Derives a 32-byte key from the password using PBKDF2-HMAC-SHA256, a newly generated 32-byte random salt, and 12,800 iterations. The algorithm, salt size, key size, and iteration count are fixed; there are no options to change them or supply an existing salt.

The entire argument string is the password, including spaces and literal quote characters. There is no word parsing or quote removal inside the function. Empty input is accepted as an empty password. Input is treated as bytes without Unicode normalization.

## Practical

Use this function to generate a salt and derived key together. Keep the salt with the derived key or encrypted data if another implementation will need to reproduce the derivation. Calling the function again with the same password generates a fresh salt rather than reproducing the previous key.

## Returns

On success, two space-separated words: the salt followed by the derived key. Each word contains 64 lowercase hexadecimal characters, representing 32 bytes; the complete result is 129 characters long.

If random salt generation fails, returns the literal string `rand bytes failed [salt]`. The implementation does not check the PBKDF2 library call's return value, so a result with the normal format is not an independently checked indication that derivation succeeded.

## Restrictions

Available in every supported build. The current configure process requires OpenSSL-compatible SSL and crypto libraries and rejects explicitly disabling SSL support. The function works locally and does not require an IRC connection.

## Examples

`$pbkdf2(example password)` derives a key from the text `example password`, including its space. The salt and key vary between calls.

`$pbkdf2()` derives a key from an empty password using a fresh salt.

## Source

Registration and `function_pbkdf2()`: [source/functions.c](../../source/functions.c). Required crypto library configuration: [configure.ac](../../configure.ac).
