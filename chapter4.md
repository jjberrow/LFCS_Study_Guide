[Go Back](README.md)

### Chapter 4
* [File Ownership](#file-ownership)
* [File Permission Modes](#file-permission-modes)
* [chown](#chown)
* [chgrp](#chgrp)
* [Extended Attributes](#extended-attributes)

#### File Ownership

In Linux, every file is associated with a user and a group, which has an interest in the file and certain rights, or permissions.

The following utilities involve user and group ownership and permission settings.

| Command | Usage |
|:--------|-------|
| chown | Used to change user ownership of a file or directory |
| chgrp | Used to change group ownership |
| chmod | Used to change the permissions on the file which can be done seperately for owner, group, and other |

#### File Permission Modes

Files have three kinds of permissions: read, write, and execute. These permissions affect three groups of owners: user, group, and other.

As a result, you have three groups of three permissions:

    rwx:	rwx:	rwx:
    u:		g:		o:

There are many different ways to use chmod. For example, if you have a file with read/write permissions for user and group, and read permissions for other:

    $ ls -l test1
    -rw-rw-r-- 1 coop coop 1601 Mar 9 15:04 test1

and you wanted to give owner and other execute permissions and remove write permissions from group:

    $ chmod uo+x,g-w test1
    $ ls -l test1
    -rwxr--r-x 1 coop coop 1601 Mar 9 15:04 test1

The shorthand lets you set all permissions in one step with a simple algorithm. A single digit (sum of following numbers) will represent the permission and the position will represent the type of owner:

* 4: read
* 2: write
* 1: execute

To achieve the same as above, you could do:

    $ chmod 745 test1
    $ ls -l test1
    -rwxr--r-x 1 coop coop 1601 Mar 9 15:04 test1

#### chown

Using chown is pretty straight-forward. The syntax is as follows:

    $ sudo chown [user]:[group] file

The *[group]* option is completely optional, and can be completely ignored if *chgrp* is implemented for group ownership changes.

If we have a file owned by *bob* and we want to change the owner to *sally*, we could do:

    $ ls -l test1
    -rw-rw-r-- 1 bob bob 0 Mar 15 16:04 test1

    $ sudo chown sally test1
    $ ls -l test1
    -rw-rw-r-- 1 sally bob 0 Mar 15 16:04 test1

#### chgrp

chgrp is almost identical to chown, but specifies a group instead of a user. If we take the example above, and we have the user *phil* in the *dev* group along with *bob* and *sally*, and we want the three of them to have read/write permissions, we could do:

    $ ls -l test1
    -rw-rw-r-- 1 sally bob 0 Mar 15 16:04 test1

    $ sudo chgrp dev test1
    $ls -l test1
    -rw-rw-r-- 1 sally dev 0 Mar 15 16:04 test1

#### Extended Attributes

Extended Attributes associate metadata not interpreted by the filesystem with files. There are four **namespaces**: user, trusted, security, and system. The extended attributes here will focus on user space metadata.

Flag values are stored in the file inode and may be modified and set by the root user. They are viewed with **lsattr** and set with **chattr**.

| Flag | Meaning | Explanation |
|:-----|:--------|:------------|
| i | Immutable attribute | File cannot be modified. It cannot be deleted or renamed. No hard link can be created to it, and no data can be written to the file |
| a | Append-only attribute | File can only be opened in append mode for writing |
| d | No-dump attribute | File is ignored when the **dump** program is run. This is useful for swap and cache files that you don't want to back up |
| A | No-atime-update attribute | File will not modify the access time record when the file is accessed but not modified. This reduces the amount of disk I/O on the system, and can improve performance |

The syntax for **chattr** is:

    $ chattr [+|-|=mode] filename
