# LFCS Study Guide

This is imperfect studying material. It is solely designed for assisting in breaking down the domains and competencies defined on the Linux Foundation's website.

* [Chapter 1](#chapter-1)
* [Chapter 2](#chapter-2)

### Chapter 1
* [Creating Files Without Using an Editor](#creating-files-without-using-an-editor)
* [sudo](#sudo)
* [Locating Applications](#locating-applications)
* [Absolute and Relative Paths](#absolute-and-relative-paths)
* [Links](#links)
* [Standard File Streams](#standard-file-streams)
* [I/O Redirection](#io-redirection)
* [Pipes](#pipes)
* [locate](#locate)
* [Wildcards and Matching File Names](#wildcards-and-matching-file-names)
* [Finding Files in a Directory](#finding-files-in-a-directory)
* [Using find](#using-find)
* [Using Advanced find Options](#using-advanced-find-options)
* [Finding Files Based on Time and Size](#finding-files-based-on-time-and-size)
* [Viewing Files](#viewing-files)
* [touch and mkdir](#touch-and-mkdir)
* [Removing a File](#removing-a-file)
* [Renaming or Removing a Directory](#renaming-or-removing-a-directory)
* [Modifying the Command Line Prompt] (#modifying-the-command-line-prompt)


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

Both techniques will produce this file:

    line one
    line two
    line three

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

#### Viewing Files

You can use the following utilities to view files:

| Command | Usage |
|:--------|:------|
| **cat** | Used for viewing files that are not very long; it does not provide any scroll-back |
| **tac** | Used to look at a file backwards, starting with the last line |
| **less** | Used to view larger files because it is a paging program; it pauses at each screenful of text, provides scroll-back capabilities, and lets you search and navigate within the file |
| **tail** | Used to pring the last 10 lines of a file by default. You can change the number of lines by doing *-n 15* of just *-15* if you wanted to look at the last 15 lines instead of the default |
| **head** | The opposite of **tail**; by default it prints the first 10 lines of a file |

#### touch and mkdir

**touch** is often used to set or update the access, change, and modify times of files. By default it resets a file's time stamp to match the current time.

It can also be used to create an empty file:

`$ touch <filename>`

This is normally done to create an empty file as a placeholder for a later purpose.

The *-t* option allows you to set the date and timestamp of the file.

To set the time stamp to a specific time:

`$ touch -t 03201600 myfile`

This sets *myfile*'s time stamp to 4 pm, March 20th (03 20 1600).

**mkdir** is used to create a directory.

To create a sample directory called sampdir under the current directory:

`$ mkdir sampdir`

To create a sample directory called sampdir under /usr:

`$ mkdir /usr/sampdir`

To create a sample directory called sampdir under the current directory with a sub-directory called subsamp:

`$ mkdir -p sampdir/subsamp`

Removing a directory is simply done with **rmdir**. The directory must be empty or it will fail. To remove a directory and all of its cont4ents you have to do *rm -rf*.

#### Removing a file

| Command | Usage |
|:--------|:------|
| **mv** | Rename a file |
| **rm** | Remove a file |
| **rm -f** | Forcefully remove a file |
| **rm -i** | Interactively remove a file (prompt user for confirmation) |

#### Renaming or Removing a Directory

**rmdir** works only on empty directories.

While typing rm -rf is a fast and easy way to remove a whole filesystem tree recursively, it is extremely dangerous and should be used with the utmost care, especially when used by root.

| Command | Usage |
|:--------|:------|
| **mv** | Rename a directory |
| **rmdir** | Remove an empty directory |
| **rm -rf** | Forcefully remove a directory recursively |

#### Modifying the Command Line Prompt

The **PS1** variable is the character string that is displayed as the prompt on the command line. Most distributions set PS1 to a known default value. Some system administrators require the user and host system name to show up on the command line as in:

`studen@quad32 $`

This could prove useful if you are working in multiple roles and want to be always reminded of who you are and what machine you are on. The prompt above could be implemented by setting the PS1 variable to: 

`\u@\h $`

### Chapter 2
* [Introduction to RAID](#introduction-to-raid)
* [RAID Levels](#raid-levels)
* [Software RAID Configuration](#software-raid-configuration)

#### Introduction to RAID

**RAID** (Redundant Array of Independent Disks) spreads I/O over multiple disks. This can really increase performance in modern disk controller interfaces.

RAID can be implemented either in software (it is a mature part of the Linux kernel) or in hardware. If your hardware RAID is known to be of good quality, it should be more efficient than using software RAID. With the hardware implementation, the operating system is unaware that RAID is in use. For example, three 512 GB hard drives (two for data, one for parity) configured with **RAID-5** will just look like a single 1 TB disk.

One disadvantage of using hardware RAID is that if the disk controller fails, it must be replaced by a compatible controller which may not be easy to obtain. When using software RAID, the same disks can be attached to and work with any disk controller. Such considerations are more likely to be relevant for low and mid-range hardware.

Three essential features of RAID are:

* **mirroring**: writing the same data to more than one disk.
* **striping**: splitting of data to more than one disk.
* **parity**: extra data is stored to allow problem detection and repair, yielding fault tolerance.

Thus, the use of RAID can improve both performance and reliability.

#### RAID Levels

There are a number of RAID specifications of increasing complexity and use. The most commonly used levels are 0 ,1, and 5.

* **RAID 0** uses only striping. Data is spread across multiple disks. There is no redundancy and there is no stability or recovery capabilities. If any disk fails, data will be lost. Performance is improved significantly because of paraleelization of I/O tasks.
* **RAID 1** uses only mirroring; each disk has a duplicate. This is good for recovery. At least two disks are required.
* **RAID 5** uses a rotating parity stripe; a single drive failure will not cause loss of data, only a performance drop. There must be at least 3 disks.
* **RAID 6** has striped disks with dual parity; it can handle loss of two disks, and requires at least 4 disks. Because RAID 5 can impose significant stress on disks, which can lead to failures during revocery procedures, RAID 6 has become more important.
* **RAID 10** is a mirrored and striped data set. It can be viewed as RAID 1 + 0. At least 4 drives are needed.

#### Software RAID Configuration

The steps in configuring software RAID are:

1. Create partitions on each disk
2. Create RAID device with **mdadm**
3. Format RAID device
4. Add device to /etc/fstab
5. Mount RAID device
6. Capture RAID details to ensure persistence

For example:

1. Create partitions on each disk

    $ sudo fdisk /dev/sdb
    $ sudo fdisk /dev/sdc

2. Create RAID device with mdadm

    $ sudo mdadm --create /dev/md0 --level=1 --raid-disks=2 /dev/sdb3 /dev/sdc3

3. Format RAID device

    $ sudo mkfs.ext3 /dev/md0

4. Add device to /etc/fstab

    /dev/md0 /myraid ext4 defaults 0 2

5. Mount RAID device

    $ sudo mkdir /myraid
    $ sudo mount /dev/md0 /myraid

6. Capture RAID details to ensure persistence

    $ sudo bash -c "mdadm --detail --scan >> /etc/mdadm.conf"

You can examine /proc/mdstat to see the RAID status as in:

    $ cat /proc/mdstat
    Personalities : [raid1]
    md0 : active raid1 sdb3[1] sdc3[0]
    ---------- 521984 blocks [2/2]
    unused devices: <none>

To stop the RAID device, use:

    $ sudo mdadm -S /dev/md0
