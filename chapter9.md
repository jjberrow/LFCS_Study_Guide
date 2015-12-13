[Go Back](README.md)

### Chapter 9
* [User Accounts](#user-accounts)
* [useradd](#useradd)

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

#### useradd

The command:

    $ sudo useradd hugo

will create an account for user **hugo**, using default algorithms for assigning UID, GID, home directory and shell.

Specifically, the **useradd** command above causes the following steps to execute:

* The next available **UID** greater than UID_MIN (specified in */etc/login.defs*) by default is assigned as hugo's UID.
* A group called **hugo** with **GID=UID** is also created and assigned as hugo's primary group.
* A home directory **/home/hugo** is created and owned by **hugo**.
* hugo's login shell will be */bin/bash*.
* The contents of */etc/skel* is copied to */home/hugo*. By default, */etc/skel* includes startup files for bash and for the **X Window** system.
* An entry of either **!!** or **!** is placed in the password field of the */etc/shadow* file for hugo's entry, thus requiring the administrator to assign a password for the acount to be usable.

The defaults can easily be overruled by using the options available:

* **b**: specify the base directory
* **c**: add GECOS field
* **e**: set an expiration date for the account
* **g**: set the primary group of user
* **G**: set the supplimentary group(s) of user
* **k**: set alternative skel directory
* **M**: do not create the user's home directory
* **N**: do not create a group with the same name as the user
* **p**: set password for user
* **r**: create a system account
* **s**: set login shell
* **u**: set UID

All of these options can be found with:

    useradd --help



[Go Back](README.md)
