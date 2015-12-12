[Go Back](README.md)

### Chapter 8

* [rsync](#rsync)

#### rsync

rsync is a very powerful utility that can be used for backing up a directory. A simple example of backing up the *project* directory to myarchive.example.com is:

    $ rsync -r project myarchive.example.com:archives/project

It should be noted that rsync can be very destructive. As such, it is highly recommended that the following is run prior to the above command:

    $ rsync -dry-run -r project myarchive.example.com:archives/project



[Go Back](README.md)
