# LFCS Study Guide

This is imperfect studying material. It is solely designed for assisting in breaking down the domains and competencies defined on the Linux Foundation's website.

- [LFCS Study Guide](#)
    - [Command Line](#)
      - [Creating Files Without Using an Editor](#)
      - [sudo](#)
      - [Locating Applications](#)
      - [Absolute and Relative Paths](#)
      - [Links](#)
      - [Standard File Streams](#)
      - [I/O Redirection](#)
      - [Pipes](#)
      - [locate](#)
      - [Wildcards and Matching File Names](#)
      - [Finding Files in a Directory](#)
      - [Using find](#)
      - [Using Advanced find Options](#)
      - [Finding Files Based on Time and Size](#)

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

### Filesystem & storage
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

#### Standard File Streams

When commands are executed, by default there are three standard **file streams** always open for use:

* **standard input** or stdin
* **standard output** or stdout
* **standard error** or stderr

Usually stdin in your keyboard, stdout and stderr are printed on your terminal; often stderr is redirected to an error logging file. stdin is often supplied by directing input to come from a file or from the output of a previous command through a **pipe**. stdout is often redirected into a file. Since stderr is where error messages are written, often nothing will go there.

In Linux, all open files are represented internally by what are called **file descriptors**. Simply put, these are represented by numbers starting at zero.

* stdin -> 0
* stdout -> 1
* stderr -> 2

Typically, if other files are opened in addition to these three, which are opened by default, they will start at file descriptor 3 and increase from there.

#### I/O Redirection

Throught the command shell we can redirect the three standard file streams so that we can get input from either a file or another command instead of from our keyboard, and we can write output and errors to files or send them as input for subsequent commands.

For example, if we have a program called **do_something** that reads from stdin and writes to stdout and stderr, we can change ints input source by using the **<** sign followed by the name of the file to be consumed for input data:

`$ do_something < input-file`

If you want to send the output to a file, use the **>** sign:

`$ do_something > output-file`

Because stderr is not the same as stdout, error messages will still be seen on the terminal windows in the above example.

If you want to redirect stderr to a separate file, you use the stderr's file descriptor number (2) and the **>** sign, followed by the name of the file you want to hold everything the running commmand writes to stderr:

`$ do_something 2> error-file`

A special shorthand notation can be used to put anything written to file descriptor 2 in the same place as file descriptor 1: 2>&1

`$ do_something > all-output-file 2>&1`

#### Pipes

The UNIX/Linux philosophy is to have many simple and short programs (or commands) cooperate together to produce quite complex results, rather than having one complex program with many possible options and modes of operation. In order to accomplish this, extensive use of **pipes** is made; you can pipe the output of one command or program into another as its input.

`$ command1 | command2 | command3`

The above represents what we often call a **pipeline** and allows Linux to combine the actions of several commands into one. This is extraordinarily efficient because **command2** and **command3** do not have to wait for the previous pipeline commands to complete before they can begin hacking at the data in their input streams; on multi-core systems the available computing power is much better utilized and things get done quicker. In addition there is no need to save output in (temporary) files between the stages in the pipeline, which saves disk space and reduces reading and writing from disk, which is often the slowest bottleneck in getting something done.

#### locate

The **locate** utility program performs a search through a previously constructed database of files and directories on your system, matching all entries that contain a specified character string. This can sometimes result in a very long list.

To get a shorter more relevant list we can use the **grep** program as a filter; **grep** will print only the lines that contain one or more specified string as in:

`$ locate zip | grep bin`

which will list all files and directories with both "zip" and "bin" in their name. Notice the use of the pipe to chain the commands together.

**locate** utilizes the database created by another program, **updatedb**. Most Linux systems run this automatically once a day. However, you can update it at any time by just running the **updatedb** command from the command line as the root user.

#### Wildcards and Matching File Names

You can search for a filename containing specific characters using **wildcards**.

| Wildcard |      Result      |
|:---------|:-----------------|
| ? | Matches any single character |
| * | Matches any string of characters |
| [set] | Matches any character in the set of characters, for example [adf] will match any occurance of "a", "d" or "f" |
| [!set] | Matches any character not in the set of characters |

To search for files using the **?** wildcard, replace each unknown **character** with *?*, e.g. if you know only the first 2 letters are 'ba' of a 3-letter filename with an extension of .out:

`$ ls ba?.out`

To search for files using the * wildcard, replace the unknown string with  * , e.g. if you remember only that the extention was .out:

`$ ls *.out`  

#### Finding Files in a Directory

**find** is extremely useful and often-used utility program in the daily life of a sysadmin. It searches recursively for files that match specified conditions. The default path is always the present working directory.

    $ cd Documents
    $ find . -name pro\*
    ./programs.txt~
    ./programs_list.txt
    ./programs_list.txt~
    $ which firefox
    /usr/bin/firefox
    $ whereis firefox
    firefox: /usr/bin/firefox /etc/firefox /usr/lib/firefox /usr/bin/X11/firefox /usr/share/man/man1/firefox.1.gz

#### Using find

When no arguments are given, **find** list all files in the current directoy and all of its subdirectories. Commonly used options to shorten the list include:

* **-name**: only list files with a certain pattern in their name
* **-iname**: only list files with a certain pattern in their name while ignoring the case
* **-type**: restrict the results to files of a certain specified type, such as *d* for directory, *l* for symbolic link, or *f* for regular file.

Searching for files and directories named "gcc":

`$ find /usr -name gcc`

Searching only for directories named "gcc":

`$ find /usr -type d -name gcc`

Searching only for regular files named "test1":

`$ find /usr/ -type f -name test1`

#### Using Advanced find Options

Another good use of **find** is being able to run commands on the files that match your search criteria. The *-exec* option is used for this purpose.

To find and remove all files that end with .swp:

`$ find -name "*.swp" -exec rm {} ';'`

The squiggly brackets (*{ }*) is a place holder that will be filled with all the file neamse that result from the find expression, and the preceding command will be run on each one individually.

Note that you have to end the command with either ';' (including the single quotes) or \;

One can also use the *-ok* option which behaves the same as *-exec* except that find will prompt you for permission before executing the command. This makes it a good way to test your results before blindly executing any potentially dangerous commands.

#### Finding Files Based on Time and Size

It is sometimes the case that you wish to find files according to attributes such as when they were created, last used, etc, or based on their size.

Finding based on time:

`$ find / -ctime 3`

Here, *-ctime* is when the inode meta-data (i.e., file ownership, permissions, etc) last changed; it is often, but not necessarily when the file was first created. You can also search for accessed/last read (*-atime*) or modified/last written (*-mtime*) times. The number is the number of days and can be expressed as either a number (n) that means exactly that value, +n which means greater than that number, or -n which means less that that number. There are similar options for times in minutes (as in *-cmin*, *-amin*, and *-mmin*. 

Finding based on sizes:

`$ find / -size 0`

Note the size here is in 512-byte blocks, by default; you can also specify bytes (c), kilobytes (k), megabytes (M), gigabytes (G), etc. As with the time number above, file sizes can also be exact numbers (n), +n, or -n.

For example, to find files greater than 10MB in size and running a command on those files:

`$ find / -size +10M -exec command {} ';'`


