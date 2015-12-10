# LFCS Study Guide

This is imperfect studying material. It is solely designed for assisting in breaking down the domains and competencies defined on the Linux Foundation's website.

* [Chapter 1](#chapter1/README.md)
* [Chapter 2](#chapter-2)

### Chapter 2
* [Introduction to RAID](#introduction-to-raid)
* [RAID Levels](#raid-levels)
* [Software RAID Configuration](#software-raid-configuration)
* [Monitoring RAIDs](#monitoring-raids)
* [RAID Hot Spares](#raid-hot-spares)

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

#### Monitoring RAIDs

You can monitor RAID a few ways:

    $ sudo mdadm --detail /dev/md0
    $ cat /proc/mdstat

You can also use **mdmonitor** which requires appending an email address to /etc/mdadm.conf:

    MAILADDR example@email.com

mdmonitor will send an email to *example@email.com* when a problem occurs with a RAID device, such as when any of the arrays fail to start or fall into a degraded state. You can turn it on with:

    $ sudo service mdmonitor start

**On Ubuntu, the service is called mdadm instead of mdmonitor**

#### RAID Hot Spares

Since redundancy is one of the primary directives of RAID (excluding RAID0), a **hot spare** can be used.

    $ sudo mdadm --create /dev/md0 -l 5 -n3 -x 1 /dev/sda8 /dev/sda9 /dev/sda10 /dev/sda11

The **-x 1** switch tells mdadm to use one spare device.

You can test the redundancy and hot spare of your array with:

    $ sudo mdadm --fail /dev/md0 /dev/sdb2
    $ sudo mdadm --remove /dev/md0 /dev/sdb2
    $ sudo mdadm --add /dev/md0 /dev/sde2
