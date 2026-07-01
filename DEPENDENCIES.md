*** Dependencies ***
This is a list of external dependencies for EPIC6
All dependencies are essential, but epic6 will build without the "optional" ones at the loss of core functionality.

^ Dependency       ^ Status   ^ Tested with    ^ FreeBSD pkg          ^ apt-get        ^ yum              ^
| libarchive       | optional |  3.8.7         | archivers/libarchive | libarchive-dev | libarchive-devel |
| python3          | optional |  3.11.15       | lang/python3         | python3        | python3-devel    |
| pcre2            | optional |  10.47         | devel/pcre2          | libpcre2-8-0   | pcre2-devel      |
| openssl/libressl | required |  3.0.16        | (base)               | libssl-dev     | openssl-devel    |
| ncurses          | required |  6.6.20251230  | (base)               | libncurses-dev | ncurses-devel    |
| c-ares           | required |  1.34.6        | dns/c-ares           | libc-ares-dev  | c-ares devel     |
| sqlite           | required |  3.50.4        | databases/sqlite3    | libsqlite3-dev | sqlite           |
| libsodium        | required |  1.0.21        | crypto/libsodium     | libsodium-dev  | libsodium-devel  |

*** Summaries ***

 * "Tested with" means the version I'm using.  Other versions will probably work.
   If you need a specific version to work and it doesn't, let me know
 * libarchive is used to provide .zip file support for /load
 * python3 is used as a secondary programming language to provide integration to things not in ircII
 * python3 must be >= 3.8 to be recognized.
 * pcre2 allows you to use "advanced regexes" that have backrefs, which posix does't by default
 * openssl is used to provide TLS support.  Both OpenSSL 3 and LibreSSL are fully supported.
 * ncurses is used to provide terminfo support.
 * c-ares is used to provide nonblocking DNS lookups.
 * sqlite is used to provide persistent storage.
 * libsodium is used to provide strong crypto and random numbers.

