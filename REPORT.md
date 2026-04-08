# epic6 — C99 Conformance & Safety Report

**Date:** 2026-04-08  
**Codebase:** `skullydazed/epic6` (EPIC6-0.0.1, commit id 3088)  
**Scope:** All C source files under `source/` and headers under `include/`

---

## Executive Summary

epic6 is a mature IRC client that deliberately targets C99 and POSIX.1-2001
(Issue 6). Its core memory-management discipline is notably strong — a custom
allocator with magic cookies, zeroing-on-free, and panic-on-corruption gives
real defence-in-depth. The standard-library header usage is correct and
well-documented. However, the codebase never sets an explicit `-std=c99`
compiler flag, uses Variable-Length Arrays (VLAs) in one location, relies
heavily on `alloca()`/`LOCAL_COPY` (100+ sites) without overflow protection,
and contains one AI-generated file (`scrambox.c`) whose quality is
inconsistent with the rest of the project. Several smaller footguns round
out the picture.

---

## 1. Build System — C Standard Flag

`configure.ac` adds `-fno-strict-aliasing`, `-fwrapv`,
`-fno-delete-null-pointer-checks`, `-Wall -Wextra -Wpedantic`, and
(optionally) UBSan, but it **never sets `-std=c99` or `-std=gnu99`**.

The compiler therefore uses its default language mode (typically `gnu17` on
modern GCC / Clang). This means:

* GNU extensions are silently available.
* `-Wpedantic` validates against the *compiler default*, not strict C99.
* VLAs (optional in C11/C17; required in C99) are enabled by default —
  masking the VLA issues described below.
* Any future build on a compiler defaulting to a stricter mode could break.

**Recommendation:** Add `AC_PROG_CC_STDC` or pass `-std=c99` (or `-std=gnu99`
if GNU extensions are intentionally relied upon) so the language version is
explicit and reproducible.

---

## 2. C99 Feature Usage

### 2.1 Correct and Good

| Feature | Location | Notes |
|---|---|---|
| `<stdint.h>`, `<inttypes.h>` | `irc_std.h` | Fixed-width integer types used pervasively and correctly |
| `<stdbool.h>` | `irc_std.h` | `bool`/`true`/`false` used throughout |
| `PRIdMAX` / `%jd` format macros | `irc_std.h`, `ircaux.c` | Portable `intmax_t` formatting |
| `inline` | `ecdsatool.c` | Used correctly |
| `SSu` union for socket aliasing | `irc_std.h` | Correct C99 solution for strict-aliasing of `sockaddr*` types (§6.5) |
| `for`-loop scoped variables | `irc.c`, `network.c`, `scrambox.c` | `for (int i = 0; ...)` — correct C99 idiom |
| `//` line comments | `scrambox.c`, `network.c` | Functionally fine; inconsistent with the older `/* */`-only style elsewhere |

### 2.2 VLAs — Footgun

**File:** `source/functions.c`, lines 8597–8603  
**Function:** `function_pbkdf2`

```c
uint32_t  saltBytes  = 32;
unsigned char salt[saltBytes];               // VLA
char saltBytesHexOutput[saltBytes * 2 + 1]; // VLA

uint32_t  outputBytes = 32;
unsigned char output[outputBytes];           // VLA
char outputHexOutput[outputBytes * 2 + 1];  // VLA
```

Although the values happen to be `32` at runtime, the compiler emits
Variable-Length Array code because the sizes are typed as non-`const`
variables.  Problems:

1. **C11/C17 made VLAs optional.** A conforming C11 implementation may define
   `__STDC_NO_VLA__`, in which case this code will not compile.
2. **No failure path.** Stack-allocated VLAs cannot signal allocation failure;
   a large value would silently corrupt the stack.
3. **Harder to audit.** Tools and reviewers cannot statically bound the stack
   usage.

**Fix:** Replace with fixed-size arrays (`unsigned char salt[32]`) since
`saltBytes` is always 32, or heap-allocate with `new_malloc`.

---

## 3. Safety Analysis

### 3.1 Strengths

#### 3.1.1 Custom Allocator (`new_malloc` / `new_free`)

`source/ircaux.c` wraps `malloc`/`free`/`realloc` with:

* **Magic cookies** (`ALLOC_MAGIC` / `FREED_VAL`) stored in a header before
  each allocation — buffer overruns and double-frees are detected and panic.
* **Size field** in the header — allows exact `memset(0)` on free, preventing
  data leakage and use-after-free reads.
* **NULL assignment on free** — the pointer variable is zeroed after free,
  making dangling-pointer dereferences immediately obvious.
* **Size guard** — `really_new_malloc` rejects requests larger than `INT_MAX`,
  guarding against integer-underflow attacks (enabled by `-fwrapv`).
* **Valgrind annotations** — `VALGRIND_MEMPOOL_*` macros give precise
  leak-checking with no false positives.

#### 3.1.2 `malloc_sprintf`

Uses `vsnprintf` in a retry loop to compute the exact output length before
allocating, eliminating truncation and overflow for all formatted strings.

#### 3.1.3 `strlcpy` / `strlcat` usage

Used consistently throughout `ircaux.c` with correct size arguments.
A custom `strlpcat` (printf-into-strlcat) extends the pattern to formatted
output.

#### 3.1.4 Integer Overflow Mitigations

`-fwrapv` (signed integer overflow wraps rather than being UB) and
`-fno-delete-null-pointer-checks` are both set unconditionally.

#### 3.1.5 Signal Handler Discipline

Signal handlers (`source/ircsig.c`) only set `volatile sig_atomic_t` flags.
All real processing occurs in the event loop. This is correct POSIX practice
and avoids async-signal-safety pitfalls.

#### 3.1.6 Socket Aliasing

The `SSu` union (`irc_std.h`) holds all `sockaddr*` variants. Code always
writes through `ss` (the `sockaddr_storage` member) and reads back through the
appropriate typed member. This is the correct C99/POSIX idiom and avoids
strict-aliasing UB.

---

### 3.2 Issues

#### 3.2.1 `LOCAL_COPY` / `alloca` — Pervasive Stack Risk

**Definition** (`irc_std.h`):

```c
#define LOCAL_COPY(y)  strcpy((char *)alloca(strlen((y)) + 1), y)
```

`alloca()` does **not** return `NULL` on failure — stack overflow produces
silent memory corruption or a segfault with no error path. There are over
**100 call sites** across the source tree. If any user-controlled string
(e.g., a crafted IRC message) is large enough to exhaust the stack, the
behaviour is undefined and undetectable at that point.

A comment in `irc_std.h` warns *"never use LOCAL_COPY in the actual argument
list of a function call"*, acknowledging the danger while keeping the macro.

**Recommendation:** For long-lived or large copies, use `new_malloc`/`new_free`.
Reserve `LOCAL_COPY` (or plain `alloca`) only for small, bounded strings, and
consider adding a size assertion.

#### 3.2.2 Bogus `alloca` NULL Check

**File:** `source/commands.c`, line 2223

```c
if (!(args_copy = alloca(IO_BUFFER_SIZE + 1)))
```

`alloca` never returns `NULL` on POSIX systems. This check is **always false**
and gives a false sense of safety. The failure case is unreachable code.

**Fix:** Remove the check, or replace with `new_malloc` so a real failure path
exists.

#### 3.2.3 Unbounded `sprintf` in `functions.c`

**File:** `source/functions.c`, lines 8618–8621

```c
for (i = 0; i < saltBytes; i++)
    sprintf(saltBytesHexOutput + (i * 2), "%02x", (salt[i] & 0xFF));
for (i = 0; i < outputBytes; i++)
    sprintf(outputHexOutput + (i * 2), "%02x", (output[i] & 0xFF));
```

The buffers are sized correctly for these loops, so there is no actual
overflow today. However, using `sprintf` instead of `snprintf` makes this
non-obvious to readers and static-analysis tools. If `saltBytes` or
`outputBytes` were ever changed without adjusting the buffer declarations,
overflow would occur silently.

**Fix:** Use `snprintf(buf + (i * 2), remaining, "%02x", ...)` with a correct
remaining-bytes calculation, or use the hex-encoding helper already present
in `ircaux.c`.

#### 3.2.4 `MAX_PROTOCOL_SIZE` Macro — Operator Precedence Footgun

**File:** `include/irc.h`, line 79

```c
#define MAX_PROTOCOL_SIZE  IRCD_BUFFER_SIZE - 2
```

This is missing parentheses. Any arithmetic expression using this macro will
misbehave due to C macro token pasting:

```c
2 * MAX_PROTOCOL_SIZE
/* expands to: 2 * 512 - 2 = 1022 (not the intended 1020) */
```

Compare the adjacent `BIG_BUFFER_SIZE`, which is correctly parenthesised:
```c
#define BIG_BUFFER_SIZE  (IRCD_BUFFER_SIZE * 4)
```

The macro is currently used as a standalone value so no bug is triggered, but
this is a latent defect.

**Fix:**
```c
#define MAX_PROTOCOL_SIZE  (IRCD_BUFFER_SIZE - 2)
```

#### 3.2.5 `scrambox.c` — AI-Generated Code, Below Project Standards

`source/scrambox.c` is identified in its header as *"Vibe coded from Gemini
2.5 Flash in 2025"* and shows several quality issues inconsistent with the
rest of the codebase:

| Issue | Detail |
|---|---|
| `strncpy` + manual null termination | The rest of the codebase uses `strlcpy`; this file uses the error-prone strncpy-then-manually-terminate pattern four times |
| Magic-number buffer | `SCRAM_MAX_AUTH_MSG_LEN = 500` with comment "Adjust as needed" |
| Inconsistent sizing | `client_final_msg[1024]` vs `SCRAM_MAX_AUTH_MSG_LEN = 500` in the same struct |
| Dead test code | Lines 601–604 contain `server_first_msg_simulated` and `client_final_msg` that appear to be leftover from development/testing |
| C++ style comments | Uses `//` exclusively; inconsistent with the `/* */` style of all other files |

**Recommendation:** Audit and rewrite this file to match project conventions —
use `strlcpy`, remove dead code, and replace magic numbers with named
constants that match the SCRAM-SHA-512 specification.

#### 3.2.6 Signed/Unsigned Mismatch (`strlen` → `int`)

Three files assign `strlen()` (which returns `size_t`, an unsigned type) to
`int` (signed):

| File | Line | Code |
|---|---|---|
| `source/expr.c` | 47 | `int end = strlen(input);` |
| `source/logfiles.c` | 688 | `int len = strlen(arg);` |
| `source/window.c` | 8742 | `int len = strlen(arg);` |

For IRC-bounded strings this is harmless in practice, but it would be flagged
by `-Wsign-conversion` and is technically undefined behaviour for strings
longer than `INT_MAX`.

**Fix:** Use `size_t` (or `ptrdiff_t` if signed arithmetic is needed).

#### 3.2.7 OpenSSL Deprecated API Suppression

```c
/* irc_std.h */
#define OPENSSL_SUPPRESS_DEPRECATED 1
#define OPENSSL_SUPPRESS_DEPRECATED_3_0 1
```

Rather than migrating to the current OpenSSL 3.x EVP APIs, these defines
silence the deprecation warnings globally. Deprecated OpenSSL functions are
candidates for removal in future releases and may have unfixed CVEs.

**Recommendation:** Migrate affected code to the non-deprecated API surface
and remove the suppression defines.

---

## 4. Summary Table

| Category | Rating | Key Finding |
|---|---|---|
| Explicit C standard flag | ⚠️ Missing | No `-std=c99`; relies on compiler default |
| C99 header usage | ✅ Good | `<stdint.h>`, `<stdbool.h>`, etc. used correctly |
| VLAs | ⚠️ Footgun | Used in `functions.c`; should be fixed-size arrays |
| `LOCAL_COPY` / `alloca` | ⚠️ Footgun | 100+ sites; no failure path; silent stack corruption |
| Bogus `alloca` NULL check | 🐛 Bug | `alloca` never returns NULL; dead safety check |
| Memory management | ✅ Strong | Custom allocator with canaries, zeroing, size guard, Valgrind support |
| String handling | ✅ Good | `strlcpy`/`strlcat` throughout; `malloc_sprintf` is safe |
| Integer overflow | ✅ Good | `-fwrapv`; allocator size guard |
| `sprintf` (unbounded) | ⚠️ Latent risk | Two calls in `functions.c`; correct today but fragile |
| `MAX_PROTOCOL_SIZE` macro | 🐛 Latent bug | Missing parentheses; operator-precedence footgun |
| Signal handlers | ✅ Correct | Flag-only in handler; processed in event loop |
| `scrambox.c` quality | ⚠️ Poor | AI-generated; inconsistent style, dead code, magic numbers |
| Signed/unsigned mismatch | ⚠️ Minor | `strlen` → `int` in three files |
| OpenSSL deprecated API | ⚠️ Tech debt | Suppressed warnings rather than migrated |
| Global mutable state | ⚠️ Acknowledged | ~190 top-level declarations in `irc.c` (noted in source comments) |

---

## 5. Prioritised Recommendations

1. **High — add `-std=c99` (or `-std=gnu99`) to `configure.ac`** so the
   language version is explicit and the build is reproducible across compilers.

2. **High — fix VLAs in `functions.c`** — replace with fixed-size arrays;
   these are always 32 bytes at runtime.

3. **Medium — fix `MAX_PROTOCOL_SIZE` parentheses** — one-character change
   that prevents a future operator-precedence bug.

4. **Medium — remove/fix the bogus `alloca` NULL check** in `commands.c`.

5. **Medium — audit `scrambox.c`** — replace `strncpy` with `strlcpy`, remove
   dead test code, replace magic numbers.

6. **Low — migrate away from OpenSSL deprecated APIs** — remove the
   `OPENSSL_SUPPRESS_DEPRECATED` defines and update call sites.

7. **Low — fix `strlen` → `int` assignments** in `expr.c`, `logfiles.c`,
   `window.c`.

8. **Low — convert `sprintf` to `snprintf`** in `function_pbkdf2`.
