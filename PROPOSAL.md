---

Because of the fundamental changes to be expected with the Python-Rewrite,
it may be a good moment to rethink the filesystem architecture of Pi-hole.

Some alternative implementations of Pi-hole,
for example on container technology such as 'docker',
may also benefit.

This proposal is open for discussion.

Greetings, Fedora-Core

---

# Proposal of Filesystem Hierarchy Standard adherence for the Pi-hole package.

 Source:
>         Linux Foundation
>         Referenced Specifications
>         Filesystem Hierarchy Standard (FHS)
>         Version 3.0 June 3, 2015
>         refspecs.linuxfoundation.org/fhs.shtml

---

# Table of Content:

 1. package 'static' files location

 2. package 'variable' files location

 3. package 'configuration' files location

 4. package 'logging' files location

 5. Notes
 
 ---

#        Proposal:

The FHS (Filesystem Hierarchy Standard)
is the guideline for GNU/Linux installations.

Version 3.0 was published June 3, 2015
and it is the latest agreed-upon version of the standard.

While following the guideline, and this proposal,
the package 'Pi-hole' can adhere to this standard.


**START Quote from the FHS v3.0**
```
   This standard assumes that the operating system underlying an
   FHS-compliant file system supports the same basic security
   features found in most UNIX filesystems.

   It is possible to define two independent distinctions among
   files: shareable vs. unshareable and variable vs. static. In
   general, files that differ in either of these respects should
   be located in different directories. This makes it easy to
   store files with different usage characteristics on different
   filesystems.

   "Shareable" files are those that can be stored on one host and
   used on others. "Unshareable" files are those that are not
   shareable. For example, the files in user home directories are
   shareable whereas device lock files are not.

   "Static" files include binaries, libraries, documentation files
   and other files that do not change without system administrator
   intervention. "Variable" files are files that are not static.
```
**STOP Quote from the FHS v3.0**


Distinction # 1 = UNSHAREABLE vs. SHAREABLE

Distinction # 2 = STATIC vs. VARIABLE

Some examples, placed into a matrix =
```
                                                   |
                                   UNSHAREABLE     |    SHAREABLE
                                (this host only)   | (ex: can be used by other dockers)
                                                   |
 --------------------------------------------------+------------------------------------
                                                   |
         STATIC                                    |        
(read-only during normal operation)                |        
(do not change without system administrator)       |
                                                   |
                                      /etc         |    /opt
                                      /boot        |    /usr
                                                   |
 --------------------------------------------------+------------------------------------
                                                   |
         VARIABLE                                  |                
(change during normal operation)                   |                   
                                                   |
                                      /var/run     |    /var/mail
                                      /var/lock    |    /var/spool/news
                                                   |
 --------------------------------------------------+------------------------------------
```
---


# Pi-hole Section 1. package 'static' files location:

    Reference: FHS 3.13.2                   /opt/<package>

**START Quote from the FHS v3.0**
```
   /opt is reserved for the installation of add-on application
   software packages.

   A package to be installed in /opt must locate its static files
   in a separate /opt/<package> or /opt/<provider> directory tree,
   where <package> is a name that describes the software package
   and <provider> is the provider's LANANA registered name.

   Programs to be invoked by users must be located in the
   directory /opt/<package>/bin or under the /opt/<provider>
   hierarchy.

   Package files that are variable (change in normal operation)
   must be installed in /var/opt. See the section on /var/opt for
   more information.
```
**STOP Quote from the FHS v3.0**

##  {*}  Proposal for the Pi-hole package:

    /opt/pihole                   "contains the static files, needed for normal operation"

    /opt/pihole/bin               "contains the executables"

1. The Pi-hole installer/updater places the executable files into /opt/pihole/bin
2. The Pi-hole installer makes the symbolic link(s) into /usr/local/bin
for the Pi-hole executable(s) that must be available via the PATH

---

# Pi-hole Section 2. package 'variable' files location:

    Reference: FHS 5.12.1                   /var/opt/<package>

**START Quote from the FHS v3.0**
```
   Variable data of the packages in /opt must be installed in
   /var/opt/<subdir>, where <subdir> is the name of the subtree in
   /opt where the static data from an add-on software package is
   stored, except where superseded by another file in /etc. No
   structure is imposed on the internal arrangement of
   /var/opt/<subdir>.
```
**STOP Quote from the FHS v3.0**

##  {*}  Proposal for the Pi-hole package:

    /var/opt/pihole               "contains the files that change in normal operation"

    /var/opt/pihole/gravity.list  "(is one example)"

---

# Pi-hole Section 3. package 'configuration' files location:

    Reference: FHS 3.7.4                    /etc/opt/<package>

**START Quote from the FHS v3.0**
```
   The /etc hierarchy contains configuration files. A
   "configuration file" is a local file used to control the
   operation of a program; it must be static and cannot be an
   executable binary.

   It is recommended that files be stored in subdirectories of
   /etc rather than directly in /etc.

   Host-specific configuration files for add-on application
   software packages must be installed within the directory
   /etc/opt/<subdir>, where <subdir> is the name of the subtree in
   /opt where the static data from that package is stored.

   If a configuration file must reside in a different location in
   order for the package or system to function properly, it may be
   placed in a location other than /etc/opt/<subdir>.
```
**STOP Quote from the FHS v3.0**

##  {*}  Proposal for the Pi-hole package - _Alternative Option A_:

    /etc/opt/pihole               "contains the configuration files for /opt"
                                  " files OR symbolic links"

    /opt/pihole/conf              "contains the original configurations files"
                                  " copied or linked into /etc/opt/<package>"

1. The Pi-hole installer/updater places the files into /opt/pihole/conf
2. The Pi-hole installer makes the symbolic links into /etc/opt/pihole

    /opt/pihole/conf/setupVars    "(is one example)"

##  {*}  Proposal for the Pi-hole package - _Alternative Option B_:

**START Quote from the FHS v3.0**
```
   If a configuration file must reside in a different location in
   order for the package or system to function properly, it may be
   placed in a location other than /etc/opt/<subdir>.
```
**STOP Quote from the FHS v3.0**

    /opt/pihole/conf              "contains the original configurations files"

    /opt/pihole/conf/setupVars    "(is one example)"

Rationale for Pi-hole _Alternative Option B_:

1) Limit the number of <package> directories

2) Limit the number of symbolic links

=> the root administrator will appreciate it.

3) Keep away from the /etc directory

=> the root administrator will appreciate it.

4) The number of 'configuration' files for Pi-hole is very limited. 

5) Container deployment will be - more easy - more simple.

=> the docker administrator will appreciate it.

6) As long as the Pi-hole is not deployed with the distro's

native package handler (dpkg - rpm/yum - pacman - pip - ...)

the /etc directory is forbidden ground.
            
---

# Pi-hole Section 4. package 'logging' files location:

    Reference: FHS 5.10.1                   /var/log/<package>

## START Quote from the FHS v3.0
```
   This directory contains miscellaneous log files. Most logs must
   be written to this directory or an appropriate subdirectory.
```
## STOP Quote from the FHS v3.0

##  {*}  Proposal for the Pi-hole package:

    /var/log/pihole

    /var/log/pihole/pihole.log    "(is one example)"

    /var/log/pihole/logrotate.old "(Reference: man logrotate / info logrotate)"

Example of a possible logrotate configuration:

° the midnight cron job executes:
```
               logrotate /opt/pihole/conf/logrotate.conf
```
° /opt/pihole/conf/logrotate.conf contains:
```
               /var/log/pihole/pihole.log    {
                       rotate 14
                       daily
                       missingok
                       notifempty
                       delaycompress
                       compress
                       dateext
                       dateyesterday
                       copytruncate
                       olddir /var/log/pihole/logrotate.old    }
```
---

# Pi-hole Section 5. NOTES:

## 5.1 Conclusion.

In order to adhere to the Filesystem Hierarchy Standard,
only 3 <package> directories are required for the Pi-hole package:
```
                STATIC   files in /opt/pihole
                VARIABLE files in /var/opt/pihole
                LOGGING  files in /var/log/pihole
```
-    That is neat.

-    The root administrators will like it.

-    The dockers will like it.

The 'abc' of STATIC subdirectories:
```
                 STATIC supporting    files in /opt/pihole/assets
                 STATIC executable    files in /opt/pihole/bin
                 STATIC configuration files in /opt/pihole/conf
```

**NOTE:**

For the FHS purists that want to see the use of /etc/opt/pihole
a sollution is available. (_Alternative Option A_.)


## 5.2 Availability of executables via the PATH environment variable.

- Executables (package static files) are instaled in /opt/pihole/bin

- This includes the 'pihole' command, that must be available via the PATH.

(Since its use is documented for the non-root users of the Pi-hole package.)

- The PATH variable differs for root and non-root users.

The minimal definitions can be found in /etc/login.defs

- A distribution's most common PATH for root is =>
```
      /usr/local/sbin
      /usr/local/bin
      /usr/sbin
      /usr/bin
      /sbin
      /bin
```
- A distribution's most common PATH for non-root users
does NOT include all these /sbin directories,
but does AT LEAST include all these /bin directories.
```
      /usr/local/bin
      /usr/bin
      /bin
```
- Proposal: 

      For /opt/pihole/bin/pihole => make the symbolic link into /usr/local/bin

## 5.3 The git clone

- The git clone may be considered 'static package files',
since only the installer/updater is the 'owner' of the git clone.

In normal operation, the git clone is not variable, it is static.

And it is not a configuration file.

Its place is **not** in /etc/.pihole

=> gravity.sh should not copy files from the git clone,
that is a job for the installer/updater, who is the 'owner'.
     
- The git clone may be created into /opt/pihole/assets/git/.pihole

*The subdirectory /opt/pihole/assets will contain supporting files.*

## 5.4 Reactions

When confronted with new material,
two kinds of reactions are possible.

-    One reaction is REFLEX
-    One reaction is REFLEXION 

I prefer the latter.

```
    "REFLEX"
    -  Produced as an automatic response or reaction
    -  An unlearned or instinctive response to a stimulus
    American Heritage Dictionary of the English Language
```

```
    "REFLEXION - REFLECTION"
    a. Serious thinking or careful consideration
    b. A thought or an opinion resulting from such thinking or consideration
    American Heritage Dictionary of the English Language
```
---

PROPOSAL PROTOTYPE v1 - oct 2016

OPEN FOR DISCUSION

---
