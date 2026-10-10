# BitchX 1.2.1 (Modern Compatibility Edition)

![BitchX screenshot](https://github.com/user-attachments/assets/07151d29-bc51-48aa-b067-0681ecbff738)

BitchX is a free, text-based IRC (Internet Relay Chat) client for UNIX-like
systems. It descends from **ircII** and is heavily influenced by **EPIC**,
with a long history dating back to 1990.

This is the **Modern Compatibility Edition** of BitchX 1.2.1: it builds with
modern compilers and includes memory-safety fixes found through static
analysis (see [Security / hardening](#security--hardening)).

BitchX is known to compile on many systems, including FreeBSD, NetBSD,
OpenBSD, SunOS, Linux, IRIX, HP-UX, OSF/1, Ultrix, AIX, OS/2, Windows (via
Cygwin), and QNX.

## Table of contents

- [Building](#building)
- [Optional tweaks](#optional-tweaks)
- [Troubleshooting](#troubleshooting)
- [Features](#features)
- [Getting started](#getting-started)
- [Security / hardening](#security--hardening)
- [Further reading](#further-reading)

## Building

### 1. Dependencies

BitchX requires either the **terminfo** or **termcap** terminal-handling
library. This is commonly provided by the `ncurses-dev` (Debian/Ubuntu) or
`ncurses-devel` (RHEL/Fedora/openSUSE) package.

### 2. Configure

Because modern compilers are stricter than the ones used when the BitchX 1.2
source was written, set these environment variables first to handle the
legacy C standard:

```sh
export CFLAGS="-std=gnu89 -Wno-error=incompatible-pointer-types -fno-strict-aliasing -g -O2"
./configure --prefix=$HOME --with-plugins --with-tcl
```

| Option          | Purpose                                                        |
| --------------- | -------------------------------------------------------------- |
| `--prefix=$HOME`| Install to your home directory when you are not root.          |
| `--with-plugins`| Recommended if you plan to use the bundled plugins.            |
| `--with-tcl`    | Optional; only add this if you require Tcl script support.    |

Run `./configure --help` for the full list of options. You can build
out-of-tree if you prefer:

```sh
mkdir obj && cd obj && ../configure --prefix=$HOME --with-plugins
```

### 3. Compile

```sh
make            # on BSD platforms: gmake
```

You will see many warning messages — these are normal and can be ignored.
Only stop if you encounter a hard **error**.

### 4. Install

```sh
make install            # or: gmake install   (BSD)
# or, without root:
make install_local
```

This installs the `BitchX` binary to `/usr/local/bin`, or to `$HOME/bin` when
using `install_local`.

## Optional tweaks

- **Default server list:** edit `include/config.h` and look for
  `DEFAULT_SERVER` to set your default servers.
- **Graphical config:** run `make bxconf` for a console configuration
  utility.

## Troubleshooting

If configure fails with:

```
Cannot find terminfo or termcap - try installing the ncurses-dev / ncurses-devel package.
```

…install the ncurses development package for your OS and re-run configure.

If a problem cannot be resolved, please gather the **full** output of
`make` and join `#BitchX` on EFNet, open an issue on this
repository, or check the BitchX wiki FAQ.

## Features

- **Multiserver:** "last nick sent to / received from" is tracked per-server;
  `$.`, `$,`, `$B`, and the `.`/`,` message targets are per-server.
- **Half-op support:** `/HOP` and `/DEHOP` commands, the `$ishalfop()`
  scripting function, and half-op status in the default status bar.
- **Tcl support:** Tcl scripting is now part of the main distribution
  (`--with-tcl`).
- **Reworked `/NAMES` and `/SCAN`:** new `NAMES_*` and `NAMES_USER_*` formats
  (`NAMES_NICK`, `NAMES_NICK_ME`, `NAMES_USER_CHANOP`, …), plus a new
  `/SCAN -stat` sort flag for ordering by channel status.
- **New formats:** `CHANNEL_URL`, `USERMODE_OTHER`, `WHOIS_CALLERID`,
  `WHOIS_SECURE`, `WHOIS_LOGGEDIN`.
- **Plugins:** a rich plugin system activated with `--with-plugins`.
- **SASL auth:** `/SET SASL_NICK` and `/SET SASL_PASS` authenticate via
  `AUTHENTICATE PLAIN` during `CAP` negotiation (tested against Libera.Chat).

See the bundled `README` (release notes) for full details, including how to
restore the old NAMES formatting with `/FSET`.

## Getting started

```sh
BitchX irc.efnet.org
```

Use `/help` inside the client for built-in help, or read the man page
(`man BitchX`). More docs and example scripts live in
[`bitchx-docs/`](bitchx-docs) and the `script/` directory.

### SASL authentication

Networks that support SASL (e.g. Libera.Chat) can authenticate your account
at connect time instead of sending `/MSG NickServ IDENTIFY`.

Set your credentials first (case-insensitive variable names):

```irc
/SET SASL_NICK myaccount
/SET SASL_PASS mypassword
/SAVE
```

Then connect. Two ways to get TLS to Libera:

```sh
# Via a local socat tunnel (any socat version): 127.0.0.1:7001 is plain
# TCP on the loopback; socat speaks TLS to the real server for you.
BitchX 127.0.0.1:7001

# Directly, using BitchX's built-in SSL. Specify the TLS port explicitly:
# `-s` marks the next server as SSL but does not change the default port!
BitchX -s irc.libera.chat:6697
```

With both variables set, the client negotiates `CAP LS 302` on connect,
requests the `sasl` capability when the server advertises it, and completes
`AUTHENTICATE PLAIN` before finishing registration. A successful login shows
`NOTICE * :*** You are now identified for <account>` and applies any cloak
you hold. If the password is wrong the server answers
`904 SASL authentication failed` and the connection continues without an
account.

> For servers without SASL (e.g. EFNet), leaving both variables unset keeps
> the connect path identical to a stock build — no `CAP` exchange is started.

## Security / hardening

This fork applies static-analysis-driven memory-safety fixes on top of the
upstream 1.2.1 source:

- SOCKS5 proxy connect — stack buffer overflow / out-of-bounds read on
  attacker-controlled length.
- NULL-pointer dereferences in `userhost_returned`, `check_prot`,
  `userhost_ban`, `BX_massban`, `kickban`/`ban`, flood handling, ignore and
  who-parsing paths.
- Stack buffer overflow in the `/NAMES` mode-formatting code.
- Buffer truncation writing a negative numeric into a 4-byte static buffer.

Builds are verified with `gcc -fanalyzer`; the local binary is rebuilt and
kept in sync with the source.

## Further reading

- `INSTALL` — extended build instructions and compile problems.
- `README` — BitchX 1.2.1 release notes.
- `Changelog` — full change history.
- `COPYRIGHT` — licensing (BSD-style).
- `bitchx-docs/` — the bundled documentation set.