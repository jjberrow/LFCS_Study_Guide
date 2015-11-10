# LFCS Study Guide

This is imperfect studying material. It is solely designed for assisting in breaking down the domains and competencies defined on the Linux Foundation's website.

### Command Line
#### Creating Files Without Using an Editor

There are two standard ways to create a file directly from the command line and fill it with content.

The first is to use *echo*:

    $ echo line one > myfile
    $ echo line two >> myfile
    $ echo line three >> myfile

As will be covered in a later section:

    >  --> redirect output of a command to a file
    >> --> append new output to an existing file (if exists)

The second is to use *cat*:

    $ cat << EOF > myfile
    > line one
    > line two
    > line three
    > EOF
    $

Both techniques will produce [this file](1.sh).

#### sudo

*sudo* allows users to run programs using the secuirty privileges of another user, genrally root (superuser). 

If your system does not already have sudo set up and enabled, you need to do the following steps:
* As the superuser, create a configuration file to enable the user account to use sudo. Typically, this file is created in the `/etc/sudoers.d/` directory with the name of the file the same as the username. For example, let's say the username is "student". You can create the configuration file for "student" by doing this:

`# echo "student ALL=(ALL) ALL" > /etc/sudoers.d/student`

Some distributions will complain if you don't also change permissions on the file by doing:

`# chmod 440 /etc/sudoers.d/student`

#### Locating Applications

In general, executable programs should live in the following directories:

    /bin
    /usr/bin
    /sbin
    /usr/sbin
    /opt

One way of locating programs is to employ the *which* utility. For example:

    $ which diff
    /usr/bin/diff

If which doesn't find the program, *whereis* is a good alternative:

    $ whereis diff
    diff: /usr/bin/diff /usr/bin/X11/diff /usr/share/man/man1/diff.1.gz

#### Absolute and Relative Paths

There are two ways to identify paths:
1. *Absolute pathname*: An absolute pathname begins with the root directory and follows the tree, branch by branch, until it reaches the desired directory or file. Absolute paths start with a /.
2. *Relative pathname*: A relative pathname starts from the present working directory. Relative paths never start with a /.

Most of the time, using the relative path is simpler:

    $ cd ~ -> navigates to home directory of user
    $ cd ../ -> navigates to branch above

However, sometimes using the absolute path makes more sense:

    $ cd /usr/bin -> absolute
    $ cd ../../usr/bin -> relative

#### Links

The *ln* command can be used to create *hard* links and *soft* links (symlinks). These two kinds of links are very useful in UNIX-based operating systems.

Suppose the **file1** already exists. A hard link, called **file2**, can be created:

`$ ln file1 file2`

Note that two files now appear to exist. However, a closer inspection of the file listing shows that this is not quite true.

    $ ls -li file1 file2
    187031 -rw-rw-r-- 3 root test1 0 Jun 18 16:57 file1
    187031 -rw-rw-r-- 3 root test1 0 Jun 18 16:57 file2

The *-i* option to *ls* prints out in the first column the **inode** number, which is a unique quantity for each file object. This is the same for both of these files. 

**Symbolic** or soft links are created with the *-s* option as in:

    $ ln -s file1 file4
    $ ls -li file1 file4
    187931 -rw-rw-r-- 2 root  test1 0 Jun 18 16:57 file1
    131287 lrwxrwxrwx 1 test1 test1 5 Jun 21 17:18 file4 -> file1

Notice file4 no longer appears to be a regular file, and it clearly points to file1 and has a different inode number.

Symbolic links take no extra space on the filesystem. They are extrememly convenient as they can easily be modified to point to different places. An easy way to create a shortcut from your *home* directory to long pathnames is to create a symbolic link.

Unlike hard links, soft links can point to objects even on different filesystems (or partitions) which may or may not be currently available or even exist. In the case where the link does not point to a currently available or existing object, you obtain a **dangling** link.

Hard links are very useful and they save space, but you have to be careful with their use, sometimes in subtle ways. For one thing if you remove either file1 or file2 in the previous example, the **inode object** (and the remaining file name) will remain, which might be undesirable as it may lead to subtle errors later if you recreate a file of that name.

If you edit one of the files, exactly what happens depends on your editor; most editors including **vi** and **gedit** will retain the link *by default* but it is possible that modifying one of the names may break the link and result in the creation of two objects.


