[Go Back](README.md)

### Chapter 8

* [rsync](#rsync)
* [Compressing Data](#compressing-data)
* [Archiving Using tar](#archiving-using-tar)
* [Disk-to-Disk Copying](#disk-to-disk-copying)

#### rsync

rsync is a very powerful utility that can be used for backing up a directory. A simple example of backing up the *project* directory to myarchive.example.com is:

    $ rsync -r project myarchive.example.com:archives/project

It should be noted that rsync can be very destructive. As such, it is highly recommended that the following is run prior to the above command:

    $ rsync -dry-run -r project myarchive.example.com:archives/project

#### Compressing Data

As disk space and network speed are both major concerns, file compression is quite useful. The following is a table outlining some of the more common compression utilities:

| Command | Usage |
|:--------|:------|
| gzip | The most frequently used Linux compression utility |
| bzip2 | Produces files significantly smaller than those produced by gzip |
| xz | The most space efficient compression utility used in Linux |
| zip | Not common with Linux except for examining/decompressing archives from other OSes |

In addition to the above, the **tar** utility is often used to group files in an archive prior to compressing them.

#### Archiving Using tar

**tar**, historically stood for "tape archive" and was used to archive files to a magnetic tape. It allows you to create or extract files from an archive file (tarball). The modern incarnation of tar can make use of compression utilities to compress the created tarball.

| Command | Usage |
|:--------|:------|
| `$ tar xvf mydir.tar` | Extract all the files in mydir.tar into the mydir directory |
| `$ tar zcvf mydir.tar.gz mydir` | Create the archive and compress with **gzip** |
| `$ tar jcvf mydir.tar.bz2 mydir` | Create the archive and compress with **bz2** |

#### Disk-to-Disk Copying

The **dd** program is very useful for making copies of raw disk space. For example, to back up your MBR, you could enter:

     # dd if/dev/sda of=sda.mbr bs=512 count=1

You could also make a complete copy of one disk onto another:

    # dd if=/dev/sda of=/dev/sdb

A common joke with dd is that is stands for **data destroyer**, so it should be noted that its a very dangerous utility.

[Go Back](README.md)
