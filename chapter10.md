[Go Back](README.md)

### Chapter 10
* [Process Types and Priorities](#process-types-and-priorities)

#### Process Types and Priorities

Processes can be of different types according to the task being performed.

| Process Type | Description | Example |
|:-------------|:------------|:--------|
| Interactive Process | Need to be started by a user, either at a command line or through a graphical interface. | bash, firefox, top |
| Batch Processes | Automatic processes which are scheduled from and then disconnected from the terminal. These tasks are queued and work on a **FIFO** (First In/First Out) basis. | updatedb |
| Daemons | Sever processes that run continuously. Many are launched during system startup and then wait for a user or system request indicating that their service is required. | httpd, sshd, mysqld |
| Threads | Lightweight processes that are run under the umbrella of a main process, sharing memory and other resources, but are scheduled and run by the system on an individual basis. An individual thread can end without terminating the whole process and a process can create new threads at any time. Many non-trivial programs are multi-threaded. | gnome-terminal, firefox |
| Kernel Threads | Kernel tasks that users neither start nor terminate and have little control over. These may perform actions like moving a thread from one CPU to another, or making sure I/O operations to disk are completed. | kswapd0, migration, ksoftirqd |


[Go Back](README.md)
