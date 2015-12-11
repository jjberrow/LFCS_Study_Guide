[Go Back](README.md)

### Chapter 7
* [Introduction to LVM](#introduction-to-lvm)
* [LVM Utilities](#lvm-utilities)

#### Introduction to LVM

**LVM** or Logical Volume Management breaks a virtual partition into multiple chunks. Each of these chunks can be on different partitions and/or disks.

There are some advantages to using LVM:
* It is easy to change the size of logical partitions
* It is easy to add more storage space
* Things can be rearranged
* Multiple physical drives can be viewed as a single drive

The way LVM works is as follows:

0. Physical disks are partitioned into one or more **disk partitions**
1. The disk partition(s) are taken into the LVM layer as **physical volumes**
2. The physical volumes are grouped together into a **volume group**
3. The volume group is divided into **logical volumes**
4. The logical volumes can be then formatted into **mountable filesystems**

The diagram below will better explain the concept:
![LVM diagram](http://i.imgur.com/dAlOl6S.png)

One of the biggest disadvantages to LVM is that is has an impact on performance due to the additional overhead.

#### LVM Utilities

As per the diagram above, the first step after partitioning your physical disks is to convert the disk partitions into physical volumes. In order to manipulate these physical volumes, a utility starting with **pv** is employed.

* **pvcreate**: Coverts a partition into a physical volume.
* **pvdisplay**: Shows the physical volumes being used.
* **pvmove**: Moves data from one physical volume within a volume group to another.
* **pvremove**: Remove a partition from a physical volume.

Once the partitions are converted to physical volumes, the physical volumes are grouped into volume groups.

Unsurprisingly, the command-line utilities used to create and manipulate volume groups start with **vg**.

* **vgcreate**: Creates volume groups.
* **vgextend**: Adds to volume groups.
* **vgreduce**: Shrinks volume groups.

Next, the volume group is partitioned into logical volumes.

* **lvcreate**: creates logical volumes within a volume group.
  * The size can be specified in bytes of number of 4MB extents.
* **lvdisplay**: reports on available logical volumes.

Filesystems are placed in logical volumes and are formatted the usual way (using **mkfs**).

The following is an example of the entire process of creating logical volumes, assuming the partitions /dev/sdb1 and /dev/sdc1 have already been created with **type 8e**:

    $ sudo pvcreate /dev/sdb1
    $ sudo pvcreate /dev/sdc1
    $ sudo vgcreate -s 16M vol_group /dev/sdb1
    $ sudo vgextend vol_group /dev/sdc1
    $ sudo lvcreate -L 50G -n log_vol vol_group
    $ sudo mkfs -t ext4 /dev/vol_group/log_vol
    $ sudo mkdir /mylvm
    $ sudo mount /dev/vol_group/log_vol /mylvm

[Go Back](README.md)
