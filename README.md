# BitchX

Installation Instructions for BitchX 1.2.1 (Modern Compatibility Edition) BitchX is known to compile on various systems including FreeBSD, NetBSD, OpenBSD, SunOS, Linux, IRIX, HP-UX, OSF/1, Ultrix, AIX, OS/2, Windows (via Cygwin), and QNX.

1. Preparation Directory: Enter the BitchX directory tree before starting. This is the most important step. Dependencies: BitchX requires either the terminfo or termcap terminal-handling library. This is commonly provided by the ncurses-dev or ncurses-devel package.

2. Configuration (Modern Compiler Workaround) -- Because modern compilers are stricter than those used when the BitchX 1.2 source was written, you must set specific environment variables to handle legacy C standards. Run the following commands in your terminal: (Bash) export CFLAGS="-std=gnu89 -Wno-error=incompatible-pointer-types -fno-strict-aliasing -g -O2".
Then run ./configure --prefix=$HOME --with-plugins --with-tcl. Explaining --prefix=$HOME: Use this if you are not root to install BitchX to your home directory.--with-plugins: Recommended if you plan on using the various plugins distributed with BitchX.--with-tcl: Optional; add this only if you require Tcl script support.

4. Optional Tweaking Server List: You can edit include/config.h and locate DEFAULT_SERVER to change your default server list. Graphical Config: Alternatively, run make bxconf to use a graphical configuration utility.

5. Compilation Command: Execute make inside the BitchX directory (on BSD platforms, use gmake). Warnings: You will see many Warning messages; these are normal and can be ignored. Only stop if you encounter a hard Error.

6. Installation Command: Execute make install (or gmake install on BSD). Location: This will install the BitchX binary to /usr/local/bin or to $HOME/bin if you used the make install_local option.
