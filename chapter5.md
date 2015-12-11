[Go Back](README.md)

### Chapter 5
* [locate](#locate)
* [Finding Files in a Directory](#finding-files-in-a-directory)
* [Using find](#using-find)
* [Using Advanced find Options](#using-advanced-find-options)
* [Finding Files Based on Time and Size](#finding-files-based-on-time-and-size)

#### locate

The **locate** utility program performs a search through a previously constructed database of files and directories on your system, matching all entries that contain a specified character string. This can sometimes result in a very long list.

To get a shorter more relevant list we can use the **grep** program as a filter; **grep** will print only the lines that contain one or more specified string as in:

`$ locate zip | grep bin`

which will list all files and directories with both "zip" and "bin" in their name. Notice the use of the pipe to chain the commands together.

**locate** utilizes the database created by another program, **updatedb**. Most Linux systems run this automatically once a day. However, you can update it at any time by just running the **updatedb** command from the command line as the root user.

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

[Go Back](README.md)
