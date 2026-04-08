# Security Analysis Report: epic6 IRC Client

**Date:** 2025  
**Analyst:** GitHub Copilot Security Research  
**Repository:** epic6 IRC Client  
**Scope:** Full source code review of C source files under `source/` and `include/`

---

## Executive Summary

This report documents security vulnerabilities found in the epic6 IRC client codebase through static source code analysis. The most critical finding is that **SSL/TLS certificate validation is disabled by default**, making every SSL-protected IRC connection susceptible to man-in-the-middle (MitM) attacks out of the box. Additional medium and low severity issues include weak cryptographic defaults, sensitive data exposure via hooks, environment variable injection, and dangerous low-level patterns (alloca-based stack allocation, SHA-1 certificate fingerprinting).

---

## Table of Contents

1. [SSL/TLS Issues](#1-ssltls-issues)
2. [Sensitive Data Exposure](#2-sensitive-data-exposure)
3. [Environment Variable Injection](#3-environment-variable-injection)
4. [Cryptographic Weaknesses](#4-cryptographic-weaknesses)
5. [CTCP Handling](#5-ctcp-handling)
6. [IRC Protocol Issues](#6-irc-protocol-issues)
7. [Memory Safety Patterns](#7-memory-safety-patterns)
8. [Scripting Engine](#8-scripting-engine)
9. [Informational Findings](#9-informational-findings)
10. [Summary Table](#10-summary-table)

---

## 1. SSL/TLS Issues

### VULN-001 — Invalid SSL Certificates Accepted by Default (HIGH)

**File:** `include/config.h:42`, `source/server.c:1893–1914`

**Type:** SSL/TLS — Insecure Default

**Description:**  
The compile-time default for `ACCEPT_INVALID_SSL_CERT` is `1` (ON):

```c
// include/config.h:42
#define DEFAULT_ACCEPT_INVALID_SSL_CERT 1
```

At runtime, when an SSL connection is established and `verify_error` (self-signed cert or hostname mismatch) is set, the code at `server.c:1893` checks this setting:

```c
// source/server.c:1893–1912
if (verify_error)
{
    if (!other_error && get_int_var(ACCEPT_INVALID_SSL_CERT_VAR))
    {
        syserr(i, "The SSL certificate for server %d has problems, "
                  "but /SET ACCEPT_INVALID_SSL_CERT is ON", i);
        s->accept_cert = 1;   // connection proceeds!
    }
    else
    {
        s->accept_cert = 0;   // only rejected if "other_error" is set
    }
}
```

By default, any SSL certificate with self-signed or hostname-mismatch errors is silently accepted. This means **every default SSL connection is susceptible to MitM attacks**.

**Impact:**  
An attacker on the network path can intercept all IRC communications protected by SSL, including passwords, private messages, and channel content. Users are likely unaware because there is no visual warning beyond a log message.

**Suggested Fix:**  
Change the default to `0` (OFF). Users who need to connect to IRC servers with self-signed certificates should explicitly opt in:

```c
// include/config.h:42
#define DEFAULT_ACCEPT_INVALID_SSL_CERT 0
```

Additionally, add a clear, visible warning (not just a `syserr()`) when an invalid certificate is encountered, and prompt the user before proceeding.

---

### VULN-002 — SSL verify_callback Always Returns 1, Never Aborts Handshake (MEDIUM)

**File:** `source/ssl.c:1280–1336`

**Type:** SSL/TLS — Verification Policy

**Description:**  
The SSL verification callback (`verify_callback`) is registered with `SSL_CTX_set_verify(ctx, SSL_VERIFY_PEER | SSL_VERIFY_CLIENT_ONCE, verify_callback)`, but it **always returns 1** regardless of errors:

```c
// source/ssl.c:1280–1336
static int verify_callback (int preverify_ok, X509_STORE_CTX *ctx_)
{
    // ... collects errors into metadata ...

    if (preverify_ok == 0)
    {
        mydata->md.ssl_cert_errors = new_ssl_cert_error(...);
        mydata->md.verify_error = 1;
        // ...
        say("ssl cert verify error: err=%d ...");
    }

    return 1;  // ALWAYS returns 1 — never aborts the handshake
}
```

OpenSSL allows a `return 0` here to abort the handshake immediately. Instead, the callback defers all policy decisions to the application layer (`server.c`), and relies on the `ACCEPT_INVALID_SSL_CERT` setting (which defaults to ON per VULN-001).

**Impact:**  
Even with `ACCEPT_INVALID_SSL_CERT` set to OFF, the handshake itself is never aborted at the TLS layer; the client completes the handshake and then disconnects. This design means there is a window where cryptographic material is exchanged with an untrusted server.

**Suggested Fix:**  
Consider returning `0` from the callback for `other_error` cases (e.g. expired certificates, unknown CA) to abort the TLS handshake immediately. Separately, for self-signed/hostname errors, the deferred policy check approach is acceptable but relies on VULN-001 being fixed.

---

### VULN-003 — Legacy SSL Method `SSLv23_client_method()` Used (LOW)

**File:** `source/ssl.c:309`

**Type:** SSL/TLS — Weak Protocol

**Description:**  
The SSL context is created using the deprecated `SSLv23_client_method()`:

```c
// source/ssl.c:309
ctx = SSL_CTX_new(SSLv23_client_method());
```

While modern OpenSSL maps this to `TLS_client_method()` and protocol negotiation should yield TLSv1.2+, no explicit minimum version is set with `SSL_CTX_set_min_proto_version()` and no weak protocols are explicitly disabled with `SSL_CTX_set_options(ctx, SSL_OP_NO_SSLv2 | SSL_OP_NO_SSLv3 | SSL_OP_NO_TLSv1 | SSL_OP_NO_TLSv1_1)`.

**Impact:**  
On older OpenSSL builds or with certain configurations, SSLv3 and TLSv1.0/1.1 (all known-weak) may be negotiated. This enables POODLE, BEAST, and other downgrade attacks.

**Suggested Fix:**

```c
// source/ssl.c — after SSL_CTX_new()
ctx = SSL_CTX_new(TLS_client_method());
SSL_CTX_set_min_proto_version(ctx, TLS1_2_VERSION);
SSL_CTX_set_options(ctx, SSL_OP_NO_SSLv2 | SSL_OP_NO_SSLv3 |
                         SSL_OP_NO_TLSv1 | SSL_OP_NO_TLSv1_1);
```

---

### VULN-004 — SHA-1 Used for Certificate Fingerprinting (LOW)

**File:** `source/ssl.c:867`

**Type:** Cryptographic Weakness

**Description:**  
Certificate fingerprints are computed using SHA-1:

```c
// source/ssl.c:867
X509_digest(server_cert, EVP_sha1(), h, &hlen);
```

SHA-1 is considered cryptographically broken for collision resistance. Certificate pinning based on SHA-1 fingerprints can be bypassed with a crafted certificate sharing the same SHA-1 hash.

**Impact:**  
Certificate pinning based on SHA-1 fingerprints provides a false sense of security. If a user pins a server by its SHA-1 fingerprint, a sophisticated attacker can present a colliding forged certificate.

**Suggested Fix:**  
Use SHA-256 or SHA-512:

```c
X509_digest(server_cert, EVP_sha256(), h, &hlen);
```

The `htext` buffer (1024 bytes) is large enough for a SHA-256 digest (32 bytes → 95 printable chars with colons).

---

## 2. Sensitive Data Exposure

### VULN-005 — OPER Password Exposed via `SEND_TO_SERVER` Hook (MEDIUM)

**File:** `source/commands.c:2190`, `source/server.c:2224`

**Type:** Sensitive Data Exposure

**Description:**  
The `/OPER` command constructs its IRC message and passes it to `send_to_server()`:

```c
// source/commands.c:2190
send_to_server("OPER %s %s", nick, password);
```

Before the message is sent to the network, it is passed verbatim (password and all) to the `SEND_TO_SERVER_LIST` hook:

```c
// source/server.c:2224
if (do_hook(SEND_TO_SERVER_LIST, "%d %d %s", from_server, des, buffer))
    send_to_aserver_raw(refnum, strlen(buffer), buffer);
```

The `buffer` at this point contains `OPER nick password`. Any ircII script that hooks `/on SEND_TO_SERVER` can read the operator password in plaintext.

**Impact:**  
A malicious or compromised script loaded by the user can silently harvest `OPER` credentials. This affects all scripts loaded at startup (via `~/.epicrc`) and any scripts loaded via `/LOAD`.

**Suggested Fix:**  
Strip or redact the password from the hook notification for sensitive commands (`OPER`, `PASS`, `NS IDENTIFY`, etc.), or add a separate hook (e.g. `PRE_SEND_TO_SERVER_LIST`) that is fired before credential-bearing commands.

---

### VULN-006 — PBKDF2 Debug Output Always Displayed (LOW)

**File:** `source/functions.c:8615`

**Type:** Information Disclosure

**Description:**  
Every call to the `$pbkdf2()` function unconditionally calls `yell()` to print debug output:

```c
// source/functions.c:8615
yell("Doing %d iterations", (int)iterations);
```

`yell()` outputs to the screen and may be logged. The presence of this debug call reveals:
1. That the user is performing a PBKDF2 key derivation (i.e., using password-based encryption)
2. The exact iteration count (12,800)

**Impact:**  
An observer with access to screen output or logs learns when PBKDF2 operations occur. The iteration count leak is minor but contributes to VULN-007.

**Suggested Fix:**  
Remove the `yell()` call from the production code path, or replace it with `debug(DEBUG_CRYPTO, ...)`:

```c
// Remove or replace:
// yell("Doing %d iterations", (int)iterations);
debug(DEBUG_CRYPTO, "pbkdf2: doing %d iterations", (int)iterations);
```

---

## 3. Environment Variable Injection

### VULN-007 — `IRCUMODE` Environment Variable Not Validated (LOW)

**File:** `source/irc.c:686–687`, `source/server.c:2928–2934`

**Type:** Environment Variable Injection

**Description:**  
The `IRCUMODE` environment variable is read at startup and stored without any validation:

```c
// source/irc.c:686–687
if ((cptr = getenv("IRCUMODE")))
    send_umode = malloc_strdup(cptr);
```

On successful server registration, this string is sent directly in a `MODE` command:

```c
// source/server.c:2928–2934
modes = send_umode;

if (modes && *modes)
    send_to_server("MODE %s +%s", get_server_nickname(from_server), modes);
```

An attacker who can set the environment of the epic6 process (via `LD_PRELOAD` tricks, a malicious wrapper script, or environment injection from a parent process) can set `IRCUMODE` to:
- Modes outside the `+[a-zA-Z]` pattern (e.g. contain spaces to inject arguments)
- IRC mode strings that could have unintended effects on the account

**Impact:**  
Local privilege escalation is limited, but in shared hosting or automated environments where epic6 is launched with externally-controlled environments, an attacker could force dangerous mode settings on the IRC user's account.

**Suggested Fix:**  
Validate `IRCUMODE` to contain only valid mode characters (`[a-zA-Z]`):

```c
if ((cptr = getenv("IRCUMODE"))) {
    // Only allow alphabetic characters
    int valid = 1;
    for (const char *p = cptr; *p; p++)
        if (!isalpha((unsigned char)*p)) { valid = 0; break; }
    if (valid)
        send_umode = malloc_strdup(cptr);
    else
        yell("Warning: IRCUMODE contains invalid characters, ignoring");
}
```

---

### VULN-008 — `IRC_SERVERS_FILE` Environment Variable Used Without Path Validation (LOW)

**File:** `source/server.c:1187–1189`

**Type:** Path Traversal / Environment Variable Injection

**Description:**  
When loading the default server list, the `IRC_SERVERS_FILE` environment variable is trusted without any path validation:

```c
// source/server.c:1187–1189
if ((clang_is_frustrating = getenv("IRC_SERVERS_FILE")))
    strlcpy(file_path, clang_is_frustrating, sizeof file_path);
```

Unlike other file operations in the codebase (which use `normalize_filename()` which calls `realpath()`), this path is copied directly into `file_path` and passed to `serverdesc_import_file()`. This allows path traversal sequences like `../../../etc/passwd` to be used as the server file path.

**Impact:**  
An attacker who can set `IRC_SERVERS_FILE` in the process environment can direct epic6 to read any file on the system as a server list. While this will not directly exfiltrate the file, if the file format matches a valid server-list format, it could cause epic6 to connect to attacker-controlled servers.

**Suggested Fix:**  
Apply the same path normalization used elsewhere:

```c
if ((clang_is_frustrating = getenv("IRC_SERVERS_FILE"))) {
    if (normalize_filename(clang_is_frustrating, file_path))
        return -1;  // Invalid/non-existent path
}
```

---

## 4. Cryptographic Weaknesses

### VULN-009 — Dangerously Low PBKDF2 Iteration Count (MEDIUM)

**File:** `source/functions.c:8613`

**Type:** Cryptographic Weakness — Insufficient Key Stretching

**Description:**  
The `$pbkdf2()` function performs key derivation with a hardcoded iteration count of 12,800:

```c
// source/functions.c:8613
iterations = 12800;
PKCS5_PBKDF2_HMAC(input, strlen(input), salt, saltBytes, iterations,
                  EVP_sha256(), outputBytes, output);
```

The code itself acknowledges this is inadequate:
```c
// source/functions.c:8591–8592
// Notes: We do 12,800 iterations which is wholly inadequate, but is fast.
```

NIST SP 800-132 (2023 revision) recommends a **minimum of 600,000 iterations** for PBKDF2-SHA256. 12,800 is approximately 47× too few.

**Impact:**  
Derived keys are insufficiently protected against offline brute-force attacks. An attacker who obtains the salt and derived key output can guess weak passwords orders of magnitude faster than they should.

**Suggested Fix:**  
Increase the iteration count to at least 600,000 for PBKDF2-SHA256:

```c
iterations = 600000;
```

Also consider migrating to Argon2id (via `libsodium` or `libargon2`) for new key derivations, as it is more resistant to GPU and ASIC attacks.

---

## 5. CTCP Handling

### VULN-010 — Inbound CTCP Request Flood Protection Absent (LOW)

**File:** `source/ctcp.c:590`

**Type:** IRC-Specific — Flood Protection Bypass

**Description:**  
The outbound CTCP flood protection only throttles **responses** (replies), not **requests**:

```c
// source/ctcp.c:590
if (!request && get_int_var(NO_CTCP_FLOOD_VAR))
{
    if (time(NULL) - last_ctcp_reply < 2)
    {
        last_ctcp_reply = time(NULL);
        debug(DEBUG_CTCPS, "CTCP flood reply to [%s] dropped", to);
        return;
    }
}
```

There is no corresponding guard for inbound CTCP *requests*. A malicious actor can send an unlimited number of CTCP requests from different nicks (e.g., using a botnet), and each request will be processed by the client.

**Impact:**  
Denial of service: the client is forced to process and respond to every CTCP request, consuming CPU and potentially sending many replies before the throttle kicks in. This can also trigger `/on CTCP_REQUEST` scripts unnecessarily.

**Suggested Fix:**  
Add a rate limit for inbound CTCP requests as well, independent of the response throttle. For example, track the time of last inbound CTCP from each unique sender or globally, and drop requests exceeding a configurable threshold.

---

### VULN-011 — CTCP Inline Expansion Can Grow `local_ctcp_buffer` (INFORMATIONAL)

**File:** `source/ctcp.c:474`

**Type:** Potential Logic Issue

**Description:**  
When a CTCP handler returns a string that replaces the entire message (CTCP_RESTARTABLE flag), the result replaces `local_ctcp_buffer`:

```c
// source/ctcp.c:474
snprintf(local_ctcp_buffer, sizeof(local_ctcp_buffer), "%s", ptr);
```

`local_ctcp_buffer` is `BIG_BUFFER_SIZE + 1` = 2049 bytes. If the CTCP handler's expansion is longer than 2049 bytes (which could occur with a scripted CTCP handler using `$ctcpctl(SET ...)`), the result is silently truncated. This is safe but could lead to unexpected message corruption for encryption CTCPs.

**Suggested Fix:**  
Use `malloc_strdup()` or a dynamic buffer for `local_ctcp_buffer` to avoid truncation of expanded CTCP content.

---

## 6. IRC Protocol Issues

### VULN-012 — Spurious `\n` Embedded in QUIT Message (LOW)

**File:** `source/server.c:2705`

**Type:** Protocol Injection / Bug

**Description:**  
The QUIT message is formatted with a hardcoded embedded newline:

```c
// source/server.c:2705
send_to_aserver(refnum, "QUIT :%s\n", final_message);
```

`vsend_to_aserver_with_payload()` already appends `\r\n` to every message (line 2217). This results in the wire output being:

```
QUIT :message\n\r\n
```

Many IRC servers that tolerate `\n` as a line terminator will parse this as two separate lines:
1. `QUIT :message` (terminated by `\n`)
2. `\r` (an empty command, terminated by `\n`)

**Impact:**  
Protocol confusion: the server may log a spurious empty command. On servers with strict parsers, the QUIT may fail and the connection could hang.

**Suggested Fix:**  
Remove the embedded `\n`:

```c
send_to_aserver(refnum, "QUIT :%s", final_message);
```

---

### VULN-013 — Server Message Newline Injection via `IRCUMODE` (LOW)

**File:** `source/irc.c:686`, `source/server.c:2934`

**Type:** Protocol Injection

**Description:**  
The `IRCUMODE` environment variable is used unfiltered in a `MODE` command. Since `vsnprintf` is used in `vsend_to_aserver_with_payload`, the resulting string contains the literal value of IRCUMODE. If IRCUMODE contains `\r\n` sequences, the `strlcat(buffer, "\r\n", ...)` at line 2217 would be appended *after* the embedded newline, causing the server to see two commands.

For example, `IRCUMODE="i\r\nJOIN #evil"` would cause:
```
MODE nick +i\r\nJOIN #evil\r\n
```

On the server wire: two commands: `MODE nick +i` and `JOIN #evil`.

**Impact:**  
Local attacker with ability to set environment variables can inject arbitrary IRC commands on connection.

**Suggested Fix:**  
Validate `IRCUMODE` strictly (see VULN-007 fix).

---

## 7. Memory Safety Patterns

### VULN-014 — Extensive Use of `alloca()` with Attacker-Influenced Sizes (LOW)

**File:** `include/irc_std.h:221`, `source/ctcp.c:573`, `source/functions.c:4718`, `source/functions.c:5280`, many others

**Type:** Stack Overflow / Memory Safety

**Description:**  
The `LOCAL_COPY` macro, used throughout the codebase, performs stack allocation via `alloca` with the size of a string:

```c
// include/irc_std.h:221
#define LOCAL_COPY(y)  strcpy((char *)alloca(strlen((y)) + 1), y)
```

This macro is applied to strings that may originate from the IRC server (e.g., `LOCAL_COPY(orig_line)` in `parse.c:1467`). While IRC message size is nominally limited to 512 bytes by the protocol, a malicious or misconfigured server could send longer lines. If `dgets()` allows lines longer than 512 bytes (the server line length is configurable via `/set server_line_length`), `LOCAL_COPY` could cause a stack overflow.

Additional `alloca` calls with attacker-influenced sizes:
- `ctcp.c:573`: `putbuf2 = alloca(len)` (bounded by `IRCD_BUFFER_SIZE - 12`)
- `functions.c:4718`: `alloca(strlen(input) * 3 + 1)` — tripling user string length
- `functions.c:5280`: `alloca(strlen(stuff) * 3 + 4)` — tripling user string length
- `functions.c:6140`: `alloca(input_size * 2 + 1)` — doubling user string length

If `input` is close to the maximum stack size divided by 3, these can cause silent stack corruption rather than a clean crash.

**Impact:**  
Stack overflow leading to crash (denial of service) or, on systems without stack canaries and ASLR, potential control-flow hijacking.

**Suggested Fix:**  
Replace `LOCAL_COPY` with a heap-based copy:

```c
#define LOCAL_COPY(y)  malloc_strdup(y)  // remember to free!
```

Or, for the stack-allocation use case, add a maximum size check:

```c
#define LOCAL_COPY(y)  ({ \
    size_t _len = strlen(y); \
    char *_buf = (_len < 4096) ? strcpy(alloca(_len+1), y) \
                               : panic_on_big_copy(__FILE__, __LINE__, _len); \
    _buf; })
```

For the `alloca(strlen(input) * N)` pattern, use heap allocation for large inputs.

---

## 8. Scripting Engine

### VULN-015 — Script-Level CTCP Handler Can Execute Arbitrary ircII Code on Receipt of CTCP (DESIGN RISK)

**File:** `source/ctcp.c:415–421`, `source/ctcp.c:428–436`

**Type:** Scripting Engine — Injection Risk

**Description:**  
User-defined CTCP handlers (registered via `$ctcpctl(SET <name> REQUEST {code})`) are invoked as ircII lambda functions with CTCP arguments passed from the network:

```c
// source/ctcp.c:418
malloc_sprintf(&args, "%s %s %s %s", from, to, ctcp_command, ctcp_argument);
ptr = call_lambda_function("CTCP", CTCP(i)->user_func, args);
```

The `ctcp_argument` here is an attacker-controlled string from the IRC server. It is passed as arguments to the ircII lambda. If the user's CTCP handler script does something like:

```
/ctcpctl SET MYCTCP REQUEST { eval $3 }
```

then any string in the CTCP argument is evaluated as ircII code. This is a well-known scripting engine design risk, but there is no documentation warning against this pattern, and beginner scripts may inadvertently do this.

**Impact:**  
If a script author does not carefully quote or sanitize `$3` (the CTCP argument), an attacker can craft CTCP messages that execute arbitrary ircII commands in the victim's client, leading to:
- Execution of `/exec` commands (shell commands)
- Joining/parting channels
- Sending arbitrary messages

**Suggested Fix:**  
Document clearly that CTCP handler arguments must always be treated as untrusted user input and never passed directly to `eval` or `exec`. Consider adding sandboxing for CTCP handler scripts.

---

## 9. Informational Findings

### INFO-001 — `CLOCK_FORMAT` Accepts Arbitrary `strftime` Format String (INFORMATIONAL)

**File:** `source/clock.c:89–91`

**Type:** User-Controlled Format String (strftime, not printf)

**Description:**  
The `/SET CLOCK_FORMAT` variable is passed directly to `strftime()`:

```c
// source/clock.c:89–91
if (time_format)
    strftime(current_clock, sizeof current_clock,
             time_format, &time_val);
```

Unlike `printf`-family functions, `strftime` format strings are not a code execution risk. However, a malicious `time_format` from a server (if a script blindly sets `/set clock_format` from a server message) could produce unexpected output.

**Suggested Fix:**  
No code change required. Add documentation noting that `CLOCK_FORMAT` should not be set from untrusted server content.

---

### INFO-002 — `$getenv()` Function Exposes All Environment Variables to Scripts (INFORMATIONAL)

**File:** `source/functions.c:5005–5010`

**Type:** Information Disclosure

**Description:**  
The built-in `$getenv(VAR)` function exposes any environment variable to ircII scripts:

```c
// source/functions.c:5005–5010
BUILT_IN_FUNCTION(function_getenv, input)
{
    char *env;
    GET_FUNC_ARG(env, input);
    RETURN_STR(getenv(env));
}
```

A script can call `$getenv(HOME)`, `$getenv(SSH_AUTH_SOCK)`, `$getenv(DBUS_SESSION_BUS_ADDRESS)`, etc. and leak sensitive environment data via IRC.

**Suggested Fix:**  
Consider a denylist or allowlist approach for which environment variables can be read, or at minimum document this capability clearly so users are aware when loading third-party scripts.

---

### INFO-003 — `htext` Certificate Hash Buffer Calculation Implicitly Assumes `hlen ≤ 341` (INFORMATIONAL)

**File:** `source/ssl.c:790–877`

**Type:** Potential Future Overflow

**Description:**  
The certificate hash buffer `htext` is 1024 bytes and used in a loop:

```c
// source/ssl.c:790–877
unsigned char h[256];    // digest buffer
char htext[1024];        // output string: "XX:XX:XX:..."
// ...
for (i = 0; i < hlen; i++)
{
    if (i > 0) htext[i * 3 - 1] = ':';
    snprintf(htext + (i * 3), sizeof(htext) - (i * 3), "%02x", h[i]);
}
```

For the current use (`EVP_sha1()`, 20 bytes), the maximum index is `19 * 3 + 1 = 58` — safely within 1024 bytes. If the algorithm were changed to one with a > 341-byte digest, `i * 3` would exceed 1024. The `h` buffer is 256 bytes, so `hlen` is bounded at 256, giving `i * 3 ≤ 765 < 1024`, so there is **no current overflow**.

**Suggested Fix:**  
Make the bound explicit:

```c
char htext[hlen * 3 + 1];  // or use a fixed maximum like [EVP_MAX_MD_SIZE * 3 + 1]
```

---

## 10. Summary Table

| ID | File(s) | Vulnerability Type | Severity | Status |
|---|---|---|---|---|
| VULN-001 | `include/config.h:42`, `source/server.c:1901` | SSL: Invalid certs accepted by default | **HIGH** | Open |
| VULN-002 | `source/ssl.c:1280–1336` | SSL: verify_callback always returns 1 | **MEDIUM** | Open |
| VULN-003 | `source/ssl.c:309` | SSL: Legacy SSLv23 method, no min version | **MEDIUM** | Open |
| VULN-004 | `source/ssl.c:867` | Crypto: SHA-1 cert fingerprinting | **LOW** | Open |
| VULN-005 | `source/commands.c:2190`, `source/server.c:2224` | Sensitive Data: OPER password in hook | **MEDIUM** | Open |
| VULN-006 | `source/functions.c:8615` | Info Disclosure: PBKDF2 debug output | **LOW** | Open |
| VULN-007 | `source/irc.c:686`, `source/server.c:2928–2934` | Env Injection: IRCUMODE not validated | **LOW** | Open |
| VULN-008 | `source/server.c:1187–1189` | Path Traversal: IRC_SERVERS_FILE env var | **LOW** | Open |
| VULN-009 | `source/functions.c:8613` | Crypto: PBKDF2 only 12,800 iterations | **MEDIUM** | Open |
| VULN-010 | `source/ctcp.c:590` | CTCP: No inbound request flood protection | **LOW** | Open |
| VULN-011 | `source/ctcp.c:474` | CTCP: Inline expansion truncation | **INFO** | Open |
| VULN-012 | `source/server.c:2705` | Protocol: Spurious `\n` in QUIT message | **LOW** | Open |
| VULN-013 | `source/irc.c:686`, `source/server.c:2934` | Protocol: Newline injection via IRCUMODE | **LOW** | Open |
| VULN-014 | `include/irc_std.h:221`, many | Memory: `alloca` with attacker-influenced sizes | **LOW** | Open |
| VULN-015 | `source/ctcp.c:418–432` | Scripting: CTCP args passed to lambda eval | **DESIGN** | Open |
| INFO-001 | `source/clock.c:89–91` | User-controlled strftime format | **INFO** | Open |
| INFO-002 | `source/functions.c:5005–5010` | `$getenv()` exposes all env vars to scripts | **INFO** | Open |
| INFO-003 | `source/ssl.c:790–877` | htext buffer implicit size assumption | **INFO** | Open |

---

## Recommended Priority Order

1. **VULN-001** — Change `DEFAULT_ACCEPT_INVALID_SSL_CERT` to `0` and add a visible UI warning for certificate errors. This is the single highest-impact fix.
2. **VULN-009** — Increase PBKDF2 iterations to ≥ 600,000.
3. **VULN-003** — Enforce a minimum TLS version of 1.2.
4. **VULN-005** — Prevent the OPER password from appearing in the `SEND_TO_SERVER` hook.
5. **VULN-002** — Review `verify_callback` return value policy for hard failures.
6. **VULN-004** — Migrate to SHA-256 for certificate fingerprinting.
7. **VULN-007 / VULN-013** — Validate `IRCUMODE` environment variable input.
8. **VULN-014** — Audit and limit `alloca()` usage with server-controlled sizes.
9. **VULN-008** — Apply `normalize_filename()` to `IRC_SERVERS_FILE`.
10. Remaining LOW/INFO items.

---

*End of Report*
