[Go Back](README.md)

### Chapter 10
* [Process Types](#process-types)
* [Process Commands](#process-commands)

#### Process Types

Processes can be of different types according to the task being performed.

| Process Type | Description | Example |
|:-------------|:------------|:--------|
| Interactive Process | Need to be started by a user, either at a command line or through a graphical interface. | bash, firefox, top |
| Batch Processes | Automatic processes which are scheduled from and then disconnected from the terminal. These tasks are queued and work on a **FIFO** (First In/First Out) basis. | updatedb |
| Daemons | Sever processes that run continuously. Many are launched during system startup and then wait for a user or system request indicating that their service is required. | httpd, sshd, mysqld |
| Threads | Lightweight processes that are run under the umbrella of a main process, sharing memory and other resources, but are scheduled and run by the system on an individual basis. An individual thread can end without terminating the whole process and a process can create new threads at any time. Many non-trivial programs are multi-threaded. | gnome-terminal, firefox |
| Kernel Threads | Kernel tasks that users neither start nor terminate and have little control over. These may perform actions like moving a thread from one CPU to another, or making sure I/O operations to disk are completed. | kswapd0, migration, ksoftirqd |

#### Process Commands

The **ps** command provides information about currently running processes, keyed by **PID**. ps has many options for specifying exactly which tasks to examine, what information to display about them, and precisely what output format should be used.

Without options, ps will display all processes running under the current shell. You can use the following table for basic operations. More complex options are available.

| Option | Description |
|:-------|:------------|
| -A, -e | all processes |
| -a | all with tty, except session leaders |
| a | all with tty, including other users |
| -d | all except session leaders |
| -N, --deselect | negate selection |
| r | only running processes |
| T | all processes on this terminal |
| x | processes without controlling ttys |

Another way to monitor processes is to use the **top** command. The following is an example of the output:

[top command](http://i.imgur.com/q2Shh6F.png)

While this image seems complex, we can break it down into much smaller chunks to understand its content.

**The first line** outputs a quick summary of what is happening on the system including:
* How long the system has been up
* How many users are logged in
* What the load average is.

**The second line** displays:
* The total number of processes
* The number of running processes
* The number of sleeping processes
* The number of stopped processes
* THe number of zombie processes

Comparing the number of running processes with the load average from the first line can help determine if the system has reached its capacity or a particular user is running too many processes.

**The third line** indicates how the CPU time is being divided between users (**us**) and the kernel (**sy**). It also outputs:
* Percent of user jobs running at a lower priority (ni)
* Idle mode percentage (id)
* Percent of jobs waiting (wa)
* Percent of hardware interrupts (hi)
* Percent of software interrupts (si)
* Steal time (st) is used for VMs, which has some of its idle time taken for other users.

**The fourth and fifth lines** output the RAM and swap of the system respectively.

The following table will break down how to use the interactive keys with top:

| Command | Output |
|:--------|:-------|
| t | Display/hide summary information (row 2 and 3) |
| m | Display/hide memory information (row 4 and 5) |
| A | Sort the process list by top resource consumers |
| r | Renice (change the priority of) a specific process |
| k | Kill a specific process |
| f | Enter the top configuration screen |
| o | Interactively select a new sort order in the process list |

 

[Go Back](README.md)
