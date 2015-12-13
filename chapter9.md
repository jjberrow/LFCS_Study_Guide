[Go Back](README.md)

### Chapter 9
* [User Accounts](#user-accounts)

#### User Accounts

There are a few reasons to have individual user accounts:
* Providing each user with their own individualized private space.
* Creating particular user accounts for specific dedicated purposes.
* Distiguishing privileges among users.

One special user account is for the **root** user, who is able to do anything on the system. To avoid making costly mistakes, and for security reasons, the root account should only be used when absolutely necessary.

Normal user accounts are for people who will work on the system. Some user accounts (like the **daemon** acount( exist for the purpose of allowing processes to run as a user other than root.

Each user on the system has a corresponding line in the */etc/passwd* file. This file used to contain password, but as it needs to be world readable, it was a huge security flaw. A typical /etc/passwd group of entries will look like:

    jshep:x:1000:1000:Jack Shephard:/home/jshep:/bin/bash
    kausten:x:1001:1001:Kate Austen:/home/kausten:/bin/bash
    sunkwon:x:1002:1002:Sun-Hwa Kwon:/home/sunkwon:/bin/bash

The elements here are:

* **User name**: The unique name assigned to each user.
* **User password**: The password assigned to each user.
* **UID**: A unique number assigned to the user account. The UID is used by the system for a variety of purposes, including a determination of user privileges and activity tracking.
* **GID**: Indicates the primary, principal, or default group of the user.
* **GECOS**: User comments or information (typically the user's full name).
* **Home directory**: For most users, this is a unique directory that offers a working area for the user. Normally, this directory is owned by the user.
* **Login shell**: Normally, this is a shell program such as */bin/bash* or  */bin/csh*. Sometimes, however, an alternative program is referenced here for special cases. In general, this field will accept any executable.




[Go Back](README.md)
