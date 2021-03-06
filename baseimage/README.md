# about minimum2scp/baseimage image

 * based on [minimum2scp/debian](https://github.com/minimum2scp/dockerfiles/tree/master/debian) image
 * Runs [/opt/init-wrapper/sbin/init](https://github.com/minimum2scp/dockerfiles/blob/master/baseimage/build/opt/init-wrapper/sbin/init) by default
   * /opt/init-wrapper/sbin/init invokes all scripts in /opt/init-wrapper/pre-init.d (using run-parts), and exec /sbin/init
   * /sbin/init is replaced by sysvinit-core package
   * /sbin/init invokes sshd, rsyslogd, cron daemons
   * /etc/rc.local invokes all scripts in /opt/init-wrapper/post-init.d (using run-parts)
 * ja_JP.UTF-8 locale supported. (default locale is C)
 * timezone is Asia/Tokyo
 * etckeeper installed
 * nginx-light, supervisor is installed, but disabled

## start container

```
docker run -d -p 2222:22 minimum2scp/baseimage:latest
```

## ssh login to container

ssh login to container:

```
ssh -o UserKnownHostsFile=/dev/null -o StrictHostKeyChecking=no debian@<container IP address>
```

or use published port:

```
ssh -o UserKnownHostsFile=/dev/null -o StrictHostKeyChecking=no -p 2222 debian@localhost
```

 * user "debian" is available
 * default password is "debian"
   * to change password, set environment variable `USER_PASSWORD` (see /opt/init-wrapper/pre-init.d/04-chpass-and-sshkey)
   * to set ssh public key into `~/.ssh/authorized_keys`, set environment variable `USER_SSH_KEY_URI` (see /opt/init-wrapper/pre-init.d/04-chpass-and-sshkey)
 * user "debian" can use sudo command without password
 * `id debian`: `uid=2000(debian) gid=2000(debian) groups=2000(debian),4(adm),27(sudo)`

## processes

```
UID        PID  PPID  C STIME TTY      STAT   TIME CMD
root         1     0  0 01:52 ?        Ss     0:00 init [2]
root        37     1  0 01:52 ?        Ssl    0:00 /usr/sbin/rsyslogd
root        62     1  0 01:52 ?        Ss     0:00 /usr/sbin/cron
root        73     1  0 01:52 ?        Ss     0:00 /usr/sbin/sshd
root        80    73  0 01:52 ?        Ss     0:00  \_ sshd: debian [priv]
debian      82    80  0 01:52 ?        S      0:00      \_ sshd: debian@pts/0
debian      83    82  0 01:52 pts/0    Ss     0:00          \_ -bash
debian      89    83  0 01:52 pts/0    R+     0:00              \_ ps -ef fww
```

## ports

 * TCP/22: sshd

## packages

difference between minimum2scp/debian:latest and minimum2scp/baseimage:latest

```
% docker run --rm minimum2scp/debian:latest bash -c 'export LANG=C; export COLUMNS=120; dpkg -l' > /tmp/docker-diff.20150217-4933-7toqbr.out
% docker run --rm minimum2scp/baseimage:latest bash -c 'export LANG=C; export COLUMNS=120; dpkg -l' > /tmp/docker-diff.20150217-4933-mydu16.out
% diff -u /tmp/docker-diff.20150217-4933-7toqbr.out /tmp/docker-diff.20150217-4933-mydu16.out
--- /tmp/docker-diff.20150217-4933-7toqbr.out	2015-02-17 00:44:48.190001407 +0900
+++ /tmp/docker-diff.20150217-4933-mydu16.out	2015-02-17 00:44:48.821998692 +0900
@@ -6,11 +6,16 @@
 ii  acl                      2.2.52-2          amd64             Access control list utilities
 ii  adduser                  3.113+nmu3        all               add and remove users and groups
 ii  apt                      1.0.9.6           amd64             commandline package manager
+ii  apt-utils                1.0.9.6           amd64             package management related utility programs
 ii  base-files               8                 amd64             Debian base system miscellaneous files
 ii  base-passwd              3.5.37            amd64             Debian base system master password and group files
 ii  bash                     4.3-12            amd64             GNU Bourne Again SHell
+ii  bsdmainutils             9.0.6             amd64             collection of more utilities from FreeBSD
 ii  bsdutils                 1:2.25.2-5        amd64             basic utilities from 4.4BSD-Lite
+ii  ca-certificates          20141019          all               Common CA certificates
 ii  coreutils                8.23-3            amd64             GNU core utilities
+ii  cron                     3.0pl1-127        amd64             process scheduling daemon
+ii  curl                     7.38.0-4          amd64             command line tool for transferring data with URL synt
 ii  dash                     0.5.7-4+b1        amd64             POSIX-compliant shell
 ii  debconf                  1.5.55            all               Debian configuration management system
 ii  debconf-i18n             1.5.55            all               full internationalization support for debconf
@@ -21,26 +26,33 @@
 ii  dpkg                     1.17.23           amd64             Debian package management system
 ii  e2fslibs:amd64           1.42.12-1         amd64             ext2/ext3/ext4 file system libraries
 ii  e2fsprogs                1.42.12-1         amd64             ext2/ext3/ext4 file system utilities
+ii  etckeeper                1.16              all               store /etc in git, mercurial, bzr or darcs
 ii  findutils                4.4.2-9+b1        amd64             utilities for finding files--find, xargs
 ii  gcc-4.7-base:amd64       4.7.4-3           amd64             GCC, the GNU Compiler Collection (base package)
 ii  gcc-4.8-base:amd64       4.8.4-1           amd64             GCC, the GNU Compiler Collection (base package)
 ii  gcc-4.9-base:amd64       4.9.2-10          amd64             GCC, the GNU Compiler Collection (base package)
+ii  git                      1:2.1.4-2.1       amd64             fast, scalable, distributed revision control system
+ii  git-man                  1:2.1.4-2.1       all               fast, scalable, distributed revision control system (
 ii  gnupg                    1.4.18-6          amd64             GNU privacy guard - a free PGP replacement
 ii  gpgv                     1.4.18-6          amd64             GNU privacy guard - signature verification tool
-ii  grep                     2.20-4            amd64             GNU grep, egrep and fgrep
+ii  grep                     2.20-4.1          amd64             GNU grep, egrep and fgrep
+ii  groff-base               1.22.3-1          amd64             GNU troff text-formatting system (base system compone
 ii  gzip                     1.6-4             amd64             GNU compression utilities
 ii  hostname                 3.15              amd64             utility to set/show the host name or domain name
 ii  inetutils-ping           2:1.9.2.39.3a460- amd64             ICMP echo tool
 ii  init                     1.22              amd64             System-V-like init utilities - metapackage
+ii  init-system-helpers      1.22              all               helper tools for all init systems
 ii  initscripts              2.88dsf-58        amd64             scripts for initializing and shutting down the system
 ii  insserv                  1.14.0-5          amd64             boot sequence organizer using LSB init.d script depen
 ii  iproute2                 3.16.0-2          amd64             networking and traffic control tools
 ii  libacl1:amd64            2.2.52-2          amd64             Access control list shared library
+ii  libapt-inst1.5:amd64     1.0.9.6           amd64             deb package format runtime library
 ii  libapt-pkg4.12:amd64     1.0.9.6           amd64             package management runtime library
 ii  libattr1:amd64           1:2.4.47-2        amd64             Extended attribute shared library
 ii  libaudit-common          1:2.4-1           all               Dynamic library for security auditing - common files
 ii  libaudit1:amd64          1:2.4-1+b1        amd64             Dynamic library for security auditing
 ii  libblkid1:amd64          2.25.2-5          amd64             block device id library
+ii  libbsd0:amd64            0.7.0-2           amd64             utility functions from BSD systems - shared library
 ii  libbz2-1.0:amd64         1.0.6-7+b2        amd64             high-quality block-sorting file compressor library - 
 ii  libc-bin                 2.19-15           amd64             GNU C Library: Binaries
 ii  libc6:amd64              2.19-15           amd64             GNU C Library: Shared libraries
@@ -48,34 +60,71 @@
 ii  libcap2-bin              1:2.24-6          amd64             POSIX 1003.1e capabilities (utilities)
 ii  libcomerr2:amd64         1.42.12-1         amd64             common error description library
 ii  libcryptsetup4:amd64     2:1.6.6-5         amd64             disk encryption support - shared library
+ii  libcurl3:amd64           7.38.0-4          amd64             easy-to-use client-side URL transfer library (OpenSSL
+ii  libcurl3-gnutls:amd64    7.38.0-4          amd64             easy-to-use client-side URL transfer library (GnuTLS 
 ii  libdb5.3:amd64           5.3.28-9          amd64             Berkeley v5.3 Database Libraries [runtime]
 ii  libdebconfclient0:amd64  0.192             amd64             Debian Configuration Management System (C-implementat
 ii  libdevmapper1.02.1:amd64 2:1.02.90-2       amd64             Linux Kernel Device Mapper userspace library
+ii  libedit2:amd64           3.1-20140620-2    amd64             BSD editline and history libraries
+ii  liberror-perl            0.17-1.1          all               Perl module for error/exception handling in an OO-ish
+ii  libestr0                 0.1.9-1.1         amd64             Helper functions for handling strings (lib)
+ii  libexpat1:amd64          2.1.0-6+b3        amd64             XML parsing C library - runtime library
+ii  libffi6:amd64            3.1-2+b2          amd64             Foreign Function Interface library runtime
 ii  libgcc1:amd64            1:4.9.2-10        amd64             GCC support library
 ii  libgcrypt20:amd64        1.6.2-4+b1        amd64             LGPL Crypto library - runtime library
+ii  libgdbm3:amd64           1.8.3-13.1        amd64             GNU dbm database routines (runtime version)
+ii  libgmp10:amd64           2:6.0.0+dfsg-6    amd64             Multiprecision arithmetic library
+ii  libgnutls-deb0-28:amd64  3.3.8-5           amd64             GNU TLS library - main runtime library
 ii  libgpg-error0:amd64      1.17-3            amd64             library for common error values and messages in GnuPG
+ii  libgpm2:amd64            1.20.4-6.1+b2     amd64             General Purpose Mouse - shared library
+ii  libgssapi-krb5-2:amd64   1.12.1+dfsg-17    amd64             MIT Kerberos runtime libraries - krb5 GSS-API Mechani
+ii  libhogweed2:amd64        2.7.1-5           amd64             low level cryptographic library (public-key cryptos)
+ii  libidn11:amd64           1.29-1+b2         amd64             GNU Libidn library, implementation of IETF IDN specif
+ii  libjson-c2:amd64         0.11-4            amd64             JSON manipulation library - shared library
+ii  libk5crypto3:amd64       1.12.1+dfsg-17    amd64             MIT Kerberos runtime libraries - Crypto Library
+ii  libkeyutils1:amd64       1.5.9-5+b1        amd64             Linux Key Management Utilities (library)
 ii  libkmod2:amd64           18-3              amd64             libkmod shared library
+ii  libkrb5-3:amd64          1.12.1+dfsg-17    amd64             MIT Kerberos runtime libraries
+ii  libkrb5support0:amd64    1.12.1+dfsg-17    amd64             MIT Kerberos runtime libraries - Support library
+ii  libldap-2.4-2:amd64      2.4.40-4          amd64             OpenLDAP libraries
 ii  liblocale-gettext-perl   1.05-8+b1         amd64             module using libc functions for internationalization 
+ii  liblogging-stdlog0:amd64 1.0.5-1           amd64             easy to use and lightweight logging library
+ii  liblognorm1:amd64        1.0.1-3           amd64             Log normalizing library
 ii  liblzma5:amd64           5.1.1alpha+201206 amd64             XZ-format compression library
 ii  libmount1:amd64          2.25.2-5          amd64             device mounting library
 ii  libncurses5:amd64        5.9+20140913-1+b1 amd64             shared libraries for terminal handling
 ii  libncursesw5:amd64       5.9+20140913-1+b1 amd64             shared libraries for terminal handling (wide characte
+ii  libnettle4:amd64         2.7.1-5           amd64             low level cryptographic library (symmetric and one-wa
+ii  libnewt0.52:amd64        0.52.17-1+b1      amd64             Not Erik's Windowing Toolkit - text mode windowing wi
+ii  libp11-kit0:amd64        0.20.7-1          amd64             Library for loading and coordinating access to PKCS#1
 ii  libpam-modules:amd64     1.1.8-3.1         amd64             Pluggable Authentication Modules for PAM
 ii  libpam-modules-bin       1.1.8-3.1         amd64             Pluggable Authentication Modules for PAM - helper bin
 ii  libpam-runtime           1.1.8-3.1         all               Runtime support for the PAM library
 ii  libpam0g:amd64           1.1.8-3.1         amd64             Pluggable Authentication Modules library
 ii  libpcre3:amd64           2:8.35-3.3        amd64             Perl 5 Compatible Regular Expression Library - runtim
+ii  libpipeline1:amd64       1.4.0-1           amd64             pipeline manipulation library
+ii  libpopt0:amd64           1.16-10           amd64             lib for parsing cmdline parameters
 ii  libprocps3:amd64         2:3.3.9-8         amd64             library for accessing process information from /proc
+ii  libpython-stdlib:amd64   2.7.8-3           amd64             interactive high-level object-oriented language (defa
+ii  libpython2.7-minimal:amd 2.7.9-1           amd64             Minimal subset of the Python language (version 2.7)
+ii  libpython2.7-stdlib:amd6 2.7.9-1           amd64             Interactive high-level object-oriented language (stan
 ii  libreadline6:amd64       6.3-8+b3          amd64             GNU readline and history libraries, run-time librarie
+ii  librtmp1:amd64           2.4+20131018.git7 amd64             toolkit for RTMP streams (shared library)
+ii  libsasl2-2:amd64         2.1.26.dfsg1-12   amd64             Cyrus SASL - authentication abstraction library
+ii  libsasl2-modules-db:amd6 2.1.26.dfsg1-12   amd64             Cyrus SASL - pluggable authentication modules (DB)
 ii  libselinux1:amd64        2.3-2             amd64             SELinux runtime shared libraries
 ii  libsemanage-common       2.3-1             all               Common files for SELinux policy management libraries
 ii  libsemanage1:amd64       2.3-1+b1          amd64             SELinux policy management library
 ii  libsepol1:amd64          2.3-2             amd64             SELinux library for manipulating binary security poli
 ii  libslang2:amd64          2.3.0-2           amd64             S-Lang programming library - runtime version
 ii  libsmartcols1:amd64      2.25.2-5          amd64             smart column output alignment library
+ii  libsqlite3-0:amd64       3.8.7.4-1         amd64             SQLite 3 shared library
 ii  libss2:amd64             1.42.12-1         amd64             command-line interface parsing library
+ii  libssh2-1:amd64          1.4.3-4           amd64             SSH2 client-side library
+ii  libssl1.0.0:amd64        1.0.1k-1          amd64             Secure Sockets Layer toolkit - shared libraries
 ii  libstdc++6:amd64         4.9.2-10          amd64             GNU Standard C++ Library v3
 ii  libsystemd0:amd64        215-12            amd64             systemd utility library
+ii  libtasn1-6:amd64         4.2-2             amd64             Manage ASN.1 structures (runtime)
 ii  libtext-charwidth-perl   0.04-7+b3         amd64             get display widths of characters on the terminal
 ii  libtext-iconv-perl       1.7-5+b2          amd64             converts between character sets in Perl
 ii  libtext-wrapi18n-perl    0.06-7            all               internationalized substitute of Text::Wrap
@@ -84,27 +133,56 @@
 ii  libusb-0.1-4:amd64       2:0.1.12-25       amd64             userspace USB programming library
 ii  libustr-1.0-1:amd64      1.0.4-3+b2        amd64             Micro string library: shared library
 ii  libuuid1:amd64           2.25.2-5          amd64             Universally Unique ID library
+ii  libwrap0:amd64           7.6.q-25          amd64             Wietse Venema's TCP wrappers library
+ii  locales                  2.19-15           all               GNU C Library: National Language (locale) data [suppo
 ii  login                    1:4.2-3           amd64             system login tools
 ii  lsb-base                 4.1+Debian13+nmu1 all               Linux Standard Base 4.1 init script functionality
+ii  lv                       4.51-2.2          amd64             Powerful Multilingual File Viewer
+ii  man-db                   2.7.0.2-5         amd64             on-line manual pager
 ii  mawk                     1.3.3-17          amd64             a pattern scanning and text processing language
+ii  mime-support             3.58              all               MIME files 'mime.types' & 'mailcap', and support prog
 ii  mount                    2.25.2-5          amd64             Tools for mounting and manipulating filesystems
 ii  multiarch-support        2.19-15           amd64             Transitional package to ensure multiarch compatibilit
 ii  ncurses-base             5.9+20140913-1    all               basic terminal type definitions
 ii  ncurses-bin              5.9+20140913-1+b1 amd64             terminal-related programs and man pages
+ii  net-tools                1.60-26+b1        amd64             NET-3 networking toolkit
 ii  netbase                  5.3               all               Basic TCP/IP networking system
+ii  nginx-common             1.6.2-5           all               small, powerful, scalable web/proxy server - common f
+ii  nginx-light              1.6.2-5           amd64             nginx web/proxy server (basic version)
+ii  openssh-client           1:6.7p1-3         amd64             secure shell (SSH) client, for secure access to remot
+ii  openssh-server           1:6.7p1-3         amd64             secure shell (SSH) server, for secure access from rem
+ii  openssh-sftp-server      1:6.7p1-3         amd64             secure shell (SSH) sftp server module, for SFTP acces
+ii  openssl                  1.0.1k-1          amd64             Secure Sockets Layer toolkit - cryptographic utility
 ii  passwd                   1:4.2-3           amd64             change and administer password and group data
+ii  perl                     5.20.1-5          amd64             Larry Wall's Practical Extraction and Report Language
 ii  perl-base                5.20.1-5          amd64             minimal Perl system
+ii  perl-modules             5.20.1-5          all               Core Perl modules
 ii  procps                   2:3.3.9-8         amd64             /proc file system utilities
+ii  python                   2.7.8-3           amd64             interactive high-level object-oriented language (defa
+ii  python-meld3             1.0.0-1           amd64             HTML/XML templating system for Python
+ii  python-minimal           2.7.8-3           amd64             minimal subset of the Python language (default versio
+ii  python-pkg-resources     5.5.1-1           all               Package Discovery and Resource Access using pkg_resou
+ii  python2.7                2.7.9-1           amd64             Interactive high-level object-oriented language (vers
+ii  python2.7-minimal        2.7.9-1           amd64             Minimal subset of the Python language (version 2.7)
 ii  readline-common          6.3-8             all               GNU readline and history libraries, common files
+ii  rsyslog                  8.4.2-1           amd64             reliable system and kernel logging daemon
 ii  sed                      4.2.2-4+b1        amd64             The GNU sed stream editor
 ii  sensible-utils           0.0.9             all               Utilities for sensible alternative selection
 ii  startpar                 0.59-3            amd64             run processes in parallel and multiplex their output
+ii  sudo                     1.8.11p2-1.1      amd64             Provide limited super user privileges to specific use
+ii  supervisor               3.0r1-1           all               A system for controlling process state
 ii  systemd                  215-12            amd64             system and service manager
-ii  systemd-sysv             215-12            amd64             system and service manager - SysV links
 ii  sysv-rc                  2.88dsf-58        all               System-V-like runlevel change mechanism
+ii  sysvinit-core            2.88dsf-58        amd64             System-V-like init utilities
 ii  sysvinit-utils           2.88dsf-58        amd64             System-V-like utilities
 ii  tar                      1.27.1-2+b1       amd64             GNU version of the tar archiving utility
 ii  tzdata                   2015a-1           all               time zone and daylight-saving time data
 ii  udev                     215-12            amd64             /dev/ and hotplug management daemon
 ii  util-linux               2.25.2-5          amd64             Miscellaneous system utilities
+ii  vim                      2:7.4.488-5       amd64             Vi IMproved - enhanced vi editor
+ii  vim-common               2:7.4.488-5       amd64             Vi IMproved - Common files
+ii  vim-runtime              2:7.4.488-5       all               Vi IMproved - Runtime files
+ii  whiptail                 0.52.17-1+b1      amd64             Displays user-friendly dialog boxes from shell script
 ii  zlib1g:amd64             1:1.2.8.dfsg-2+b1 amd64             compression library - runtime
+ii  zsh                      5.0.7-5           amd64             shell with lots of features
+ii  zsh-common               5.0.7-5           all               architecture independent files for Zsh
```
