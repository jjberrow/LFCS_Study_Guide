[Go Back](README.md)

### Chapter 8

* [rsync](#rsync)
* [Compressing Data](#compressing-data)

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


[Go Back](README.md)
