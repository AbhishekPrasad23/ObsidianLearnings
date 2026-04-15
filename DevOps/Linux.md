

| 1   | [[#Su Sudo]]     |
| --- | ---------------- |
| 2   | [[#Proc]]        |
| 3   | [[#Inode]]       |
| 4   | [[#Ext4 vs XFS]] |
|     |                  |

Architecture

# Linux Architecture



The Linux architecture can be ==depicted== as a layered structure. Starting from the bottom, these layers are hardware, kernel, shell, and applications.

![](https://miro.medium.com/v2/resize:fit:636/0*A6XfigfbgQgoJwE2)

Source of the image: [https://tecadmin.net/tutorial/linux-architecture](https://tecadmin.net/tutorial/linux-architecture)

1. Hardware

The lowest level of the Linux architecture is the hardware layer. This layer comprises the physical components of a computer, such as the hard drive, RAM, motherboard, CPU, network interfaces, and peripherals. These components are the tangible pieces of your system on which the rest of the architecture is built.

2. Kernel

Directly interfacing with the hardware layer is the kernel, the heart of the Linux operating system. As the core part of the OS, the kernel is responsible for low-level tasks such as disk management, task scheduling, memory management, and controlling peripherals.

The Linux kernel is a monolithic kernel, meaning it encompasses device drivers, file systems, system server calls, and more, all in a single static binary file. Because the kernel directly interacts with the system’s hardware, it’s crucial in terms of system performance and stability.

3. Shell

One layer up from the kernel is the shell. In simplest terms, the shell is a user interface that allows users to interact with the kernel. In Linux, most interactions with the shell occur in a command-line interface (CLI), where users type commands interpreted by the shell.

There are several different shells available in Linux, each with its unique features and syntax, such as the Bourne Again Shell (bash), the C Shell (csh), and the Z Shell (zsh).

4. Applications

The topmost layer of the Linux architecture consists of applications. These are the software programs that you, as the user, interact with directly. They range from system applications like file managers, text editors, and network managers, to user applications like browsers.

While applications communicate with the hardware through the kernel, they interact with the user through the shell. For instance, when you run a command to open a file in a text editor, the shell interprets your command, the kernel fetches the file from the hardware, and the text editor (application) displays it.

---


# Print lines containing "error"
awk '/error/ {print}' filename

# Print lines NOT containing "error"
awk '!/error/ {print}' filename

# Print lines where column 2 equals "value"
awk '$2 == "value" {print}' filename

# Print lines where column 1 is greater than 100
awk '$1 > 100 {print}' filename

|Command|Shows OS Version|Shows Kernel Version|Extra Info|
|---|---|---|---|
|`cat /etc/os-release`|✅|❌|Distro details|
|`lsb_release -a`|✅|❌|Clean distro info|
|`hostnamectl`|✅|✅|Hardware + kernel|
|`uname -r`|❌|✅|Kernel release only|
|`uname -a`|❌|✅|Full kernel details|
|`cat /proc/version`|❌|✅|Compiler + build info|


---

# Su Sudo

**In short:** `su` **(switch user) changes your identity to another user, often root, requiring** _**that user’s password**_**.** `sudo` **(superuser do) runs a single command with elevated privileges, requiring** _**your own password**_ **if you’re authorized. Use** `su` **when you need a full root shell or to become another user entirely, and** `sudo` **when you just need temporary elevated rights for specific commands**

---
**The `/proc` directory in Linux is a virtual filesystem that provides a window into the kernel and running processes. It doesn’t store data on disk but dynamically exposes system and process information, making it essential for monitoring, debugging, and tuning the system.**


# Proc
## 🔎 Purpose of `/proc`

- **Kernel Interface:** `/proc` acts as a bridge between **kernel space and user space**, allowing users and applications to query kernel data structures in a file-like manner.
- **Process Information:** Each running process has a subdirectory named after its **PID** (e.g., `/proc/1234`), containing details like memory usage, open file descriptors, and command-line arguments.
- **System Information:** Files like `/proc/cpuinfo`, `/proc/meminfo`, and `/proc/uptime` provide real-time details about hardware and system status.
- **Configuration Control:** The `/proc/sys` subtree allows administrators to **tune kernel parameters** (e.g., networking, memory management) without rebooting.


## 📂 Key Files and Directories

|Path|Purpose|
|---|---|
|`/proc/cpuinfo`|CPU details (model, cores, speed)|
|`/proc/meminfo`|Memory usage and availability|
|`/proc/uptime`|System uptime and idle time|
|`/proc/[PID]/`|Per-process info: environment, status, memory maps, file descriptors|
|`/proc/sys/`|Kernel tunables (modifiable with `sysctl`)|
|`/proc/loadavg`|System load averages|
|`/proc/version`|Kernel version and build info|


## ⚙️ Why It Matters

- **Monitoring:** Tools like `top`, `htop`, and `ps` rely on `/proc` to display process and system stats.
- **Debugging:** Developers can inspect process states, memory maps, and open files directly.
- **Performance Tuning:** Administrators adjust kernel parameters (e.g., TCP settings) via `/proc/sys`.
- **Security Auditing:** `/proc` reveals what processes are running and their resource usage, helping detect anomalies.



## 🚨 Risks & Considerations

- **Exposure of Sensitive Data:** `/proc` can reveal command-line arguments, environment variables, and memory maps of processes, which may leak secrets if permissions aren’t properly managed.
- **Accidental Misconfiguration:** Writing incorrect values to `/proc/sys` can destabilize the system. Always use `sysctl` with caution.
- **Performance Overhead:** Excessive polling of `/proc` (e.g., by monitoring tools) can add minor system overhead.



## ✅ Practical Example

- To check CPU info:
    
    ```bash
    cat /proc/cpuinfo
    ```
    
- To see memory usage:
    
    ```bash
    cat /proc/meminfo
    ```
    
- To adjust kernel parameter (e.g., enable IP forwarding):
    
    ```bash
    echo 1 > /proc/sys/net/ipv4/ip_forward
    ```
    

---

**In short, `/proc` is the kernel’s “control and information center,” giving users and administrators a powerful way to inspect and configure the system in real time.** [GeeksForGeeks](https://www.geeksforgeeks.org/linux-unix/proc-file-system-linux/) [linuxvox.com](https://linuxvox.com/blog/linux-proc-folder/)

---

# Inode

**An inode is a fundamental data structure in Unix-like file systems that stores metadata about a file (but not its name or actual content). It is important because it allows the operating system to efficiently manage, locate, and control files, making it central to how Linux and other Unix systems handle storage.**

## 🔎 What is an Inode?

- **Definition:** An inode (short for _Index Node_) is a record in the filesystem that describes a file or directory.
    
- **Created at filesystem initialization:** When a filesystem (like ext4) is created, a fixed number of inodes is allocated.
    
- **Unique Identifier:** Each file or directory is associated with exactly one inode number, which the system uses internally to reference it.
    

## 📂 Information Stored in an Inode

An inode contains **metadata** about a file, excluding its name and actual data. Key fields include:

- **File type** (regular file, directory, symbolic link, etc.)
    
- **Permissions** (read, write, execute for owner, group, others)
    
- **Ownership** (user ID, group ID)
    
- **Size** (in bytes)
    
- **Timestamps** (creation, modification, access)
    
- **Pointers to data blocks** (addresses of disk blocks where file content is stored)
    
- **Link count** (number of directory entries pointing to the inode)
    

## ⚙️ Why Inodes Are Important

- **File Management:** The OS uses inode numbers to track files, not filenames. Filenames are just labels mapped to inode numbers in directories.
    
- **Efficiency:** Inodes allow quick access to file metadata without scanning the entire disk.
    
- **Hard Links:** Multiple filenames can point to the same inode, enabling hard links.
    
- **Filesystem Limits:** The maximum number of files is limited by the number of inodes, not just disk space. You can run out of inodes even if free space remains.
    
- **Security & Permissions:** Inodes enforce ownership and access rights at the kernel level.

---
# Ext4 vs XFS

**Ext4 is a general-purpose, reliable filesystem widely used in Linux desktops and servers, while XFS is optimized for high-performance workloads with very large files and parallel I/O. Ext4 is simpler and more versatile, whereas XFS shines in enterprise-scale data environments.**

## 📊 Key Differences Between Ext4 and XFS

|Feature|**Ext4**|**XFS**|
|---|---|---|
|**Introduction Year**|2008 (successor to Ext3)|1994 (SGI, later ported to Linux)|
|**Max File Size**|~16 TB|~8 EB (Exabytes)|
|**Max Filesystem Size**|~1 EB|~16 EB|
|**Performance**|Balanced, good for small to medium files|Excellent for very large files and parallel workloads|
|**Journaling**|Metadata + optional data journaling|Metadata journaling only|
|**Resizing**|Supports online resize (grow/shrink)|Supports online grow only (no shrink)|
|**Use Cases**|General-purpose: desktops, laptops, small servers|Enterprise: databases, media servers, scientific data|
|**Recovery Tools**|Mature tools (`fsck.ext4`) for repair|Limited recovery tools, corruption harder to fix|
|**Default in Distros**|Default in most Linux distributions|Often used in RHEL/CentOS for enterprise workloads|