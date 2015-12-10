[Go Back](README.md)

### Chapter 3
* [LUKS](#luks)
* [Using an Encrypted Partition](#using-an-encrypted-partition)
* [Mounting at boot](#mounting-at-boot)
* [Encrypting swap](#encrypting-swap)
* [Swap](#swap)

#### LUKS

Modern Linux distributions provide block device level encryption. Usually this is done through the use of **LUKS** (Linux Unified Key Setup). Using block device encryption is recommended for portable systems (laptops, tablets, phones, etc.)

LUKS is installed on top of **cryptsetup**, a powerful encryption utility. cryptsetup can also make use of **dm-crypt** which is heavily used by LVM.

#### Using an Encrypted Partition

If we assume that the LVM partition /dev/VG/MYSECRET already exists, the following commands will set up encryption, make it available to LUKS, format it, mount it, use it and unmount it.

First, the partition is given to LUKS:

    $ sudo cryptsetup luksFormat /dev/VG/MYSECRET

You will be prompted for a passphrase that will be used to open the encrypted volume.

If your kernel does not support the default encryption method used by cryptsetup, you can examine /proc/crypto to see the methods your system supports and then supply a method:

    $ sudo cryptsetup luksFormat --cipher aes /dev/VG/MYSECRET

You can make the volume available with:

    $ sudo cryptsetup --verbose luksOpen /dev/VG/MYSECRET SECRET

where you will be prompted to supply the passphrase. You can then format the partition:

    $ sudo mkfs.ext4 /dev/mapper/SECRET

mount it:

    $ sudo mount /dev/mapper/SECRET /mnt

and then use it as if it was unencrypted. When you are done, unmount it:

    $ sudo unmount /mnt

and then remove the mapper association:

    $ sudo cryptsetup --verbose luksClose SECRET

#### Mounting at boot

To mount an encrypted partition at boot, two conditions must be satisfied:

1. Make an appropriate entry in /etc/fstab
2. Add an entry to /etc/crypttab

Typically, the entry will look something like:

    SECRET /dev/mapper/MYSECRET

#### Encrypting swap

Data written to the swap device can contain sensitive information, and as such it is important to consider security. The following steps will explain the process of encrypting a swap partition.

1. Find out the partition that is currently being used for swap, then deactivate it:

    $ cat /proc/swaps
    Filename							Type			Size		Used		Priority
    /dev/sda2							parition	4193776	0				-1

    $ sudo swapoff /dev/sda2

2. Format and make the volume available:

    $ sudo cryptsetup luksFormat /dev/sda2
    $ sudo cryptsetup luksOpen /dev/sda2 swapcrypt

3. Format the encrypted device:

    $ sudo mkswap /dev/mapper/swapcrypt

4. Now test to see that it works:

    $ sudo swapon /dev/mapper/swapcrypt
    $ cat /proc/swaps

5. Activate the swappartition on boot
  * Add a line to /etc/crypttab

    swapcrypt /dev/sda2 /dev/urandom swap,cipher=aes-cbc-essiv:sha256,size=256

  * Add an entry to /etc/fstab

    /dev/mapper/swapcrypt none swap defaults 0 0

#### Swap

Swap is how Linux employs virtual memory. Swapping typically takes place on one or more dedicated partitions or files. In most circumstances, the recommended swap size is double the size of the total RAM.

The commands involving swap are:
* **mkswap**: format a swap partition or file
* **swapon**: activate a swap partition or file
* **swapoff**: deactivate a swap partition or file


