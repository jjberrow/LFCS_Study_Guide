[Go Back](README.md)

### Chapter 6
* [Formatting Filesystems](#formatting-filesystems)
* [Checking and Fixing Filesystems](#checking-and-fixing-filesystms)
* [Network Filesystem](#network-filesystem)

#### Formatting Filesystems

Every filesystem has the ability to format a filesystem on a partition. The generic utility is **mkfs**, but this is just a front-end to filesystem specific utilities:

    $ ls -lh /sbin/mkfs*

    -rwxr-xr-x 1 root  11K Jan 10 12:00 /sbin/mkfs
    -rwxr-xr-x 1 root 181K Jan 10 12:00 /sbin/mkfs.btrfs
    -rwxr-xr-x 1 root  26K Jan 10 12:00 /sbin/mkfs.cramfs
    -rwxr-xr-x 1 root  68K Jan 10 12:00 /sbin/mkfs.ext2
    -rwxr-xr-x 1 root  68K Jan 10 12:00 /sbin/mkfs.ext3
    -rwxr-xr-x 1 root  68K Jan 10 12:00 /sbin/mkfs.ext4
    -rwxr-xr-x 1 root  68K Jan 10 12:00 /sbin/mkfs.ext4dev
    lrwxrwxrwx 1 root    7 Jan 10 12:00 /sbin/mkfs.msdos -> mkdosfs
    lrwxrwxrwx 1 root   12 Jan 10 12:00 /sbin/mkfs.ntfs -> /sbin/mkntfs
    lrwxrwxrwx 1 root    7 Jan 10 12:00 /sbin/mkfs.vfat -> mkdosfs

The general syntax for mkfs is:

    mkfs [-t fstype] [options] [device-file]

Each filesystem has its own particular options that can be set when formatting. For example, with *ext4* you can specify the journalling file size, or whether to use an external journal file.

#### Checking and Fixing Filesystems

Every filesystem has the ability to check and (hopefully) fix filesystem errors. The generic utility is **fsck**, but (just like with *mkfs*) this is just a front-end to filesystem specific utilities.

**fsck** is run automatically:
* after a set number of mounts
* after a set interval since it was last run
* after an abnormal shutdown

A fsck should only be run on unmounted filesystems.

You can force a fsck on boot:

    $ sudo touch /forcefsck
    $ sudo shutdown -r now

The general syntax of a fsck is:

    fsck [-t fstype] [options] [device-file]

#### Network Filesystem

Using **NFS** is one method for sharing data acrosss physical systems. The NFS daemon is typically stared with:

    $ sudo sevice nfs start

A text file */etc/exports* contains the directories and permissions that a host will share over NFS:

    /projects *.example.com(rw)

The above listing in the /etc/exports file will grant other hosts in the example.com domain read and write permissions to the /projects directory.

After modifying the /etc/exports file, *exportfs -av* can be used to show which directories are being allowed to be mounted using NFS.

For a client to have have access to NFS direcories, they will need to mount it:

    $ mount [servername]:/[directory] [target directory]

They can also update their */etc/fstab* file for mounting NFS persistently with something like:

    host.example.com:/projects	/projects	nfs	defaults	0 0

[Go Back](README.md)
