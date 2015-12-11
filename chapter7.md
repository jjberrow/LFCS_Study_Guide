[Go Back](README.md)

### Chapter 7
* [Introduction to LVM](#introduction-to-lvm)

#### Introduction to LVM

**LVM** or Logical Volume Management breaks a virtual partition into multiple chunks. Each of these chunks can be on different partitions and/or disks.

There are some advantages to using LVM:
* It is easy to change the size of logical partitions
* It is easy to add more storage space
* Things can be rearranged
* Multiple physical drives can be viewed as a single drive

The way LVM works is as follows:
1. One or more disk partitions are taken as **physical volumes**
2. The physical volumes are grouped together into a **volume group**
3. The volume group is divided into **logical volumes**
4. The logical volumes can be then formatted into **mountable filesystems**

The diagram below will better explain the concept:

![LVM diagram](http://i.imgur.com/dAlOl6S.png)

One of the biggest disadvantages to LVM is that is has an impact on performance due to the additional overhead.


[Go Back](README.md)
