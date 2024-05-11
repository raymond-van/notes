## Process
- fundamental unit of execution managed by OS kernel
- instance of a program
- each process has:
	- protected memory space which prevents interference from other processes
	- its own resources (CPU time, network sockets, files)
	- threads
### Threads
- the actual units of execution within a process
- multiple threads within a process enables concurrency but not parallelism (which gets enabled by distributing between CPU cores)
## Kernel
- Has direct control over the hardware: CPU, memory, disk
- Manages and allocates resources between processes
	- CPU, RAM, disk
- Provides a system call interface
	- interface is entry points for apps to request services from the OS
- Provides hardware abstraction
## Daemon
- interact directly with core OS components (kernal, networking, hardware)
- manages various system-level functions
- e.g.:
	- cron for task scheduling
	- sshd for ssh connections
	- dbus for inter-process communication
## Endianness
- refers to the order in which bytes of a data type are stored in computer memory
- Big-Endian
	- Most significant byte is stored at lowest address
- Little-Endian
	- Least significant byte is stored at lowest address
## Swap files
- **Virtual Memory Extension:** A swap file is a special file on your hard drive or solid-state drive (SSD) that acts as an extension of your computer's physical memory (RAM).
- **When RAM Gets Full:** When your RAM is running low on available space, the operating system moves inactive or less frequently used pages of data from RAM onto the hard disk/SSD into the swap file.
- **Freeing Up RAM:** This process frees up space in RAM for currently active programs or data to use.