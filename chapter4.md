[Go Back](README.md)

### Chapter 4
* [File Ownership](#file-ownership)
* [File Permission Modes](#file-permission-modes)

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


