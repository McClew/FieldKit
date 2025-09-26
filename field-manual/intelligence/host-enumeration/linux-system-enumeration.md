---
icon: linux
---

# Linux System Enumeration

## Tools

Several helper scripts such as [LinPEAS](https://github.com/carlospolop/PEASS-ng/tree/master/linPEAS) and [LinEnum](https://github.com/rebootuser/LinEnum) exist to assist with enumeration.

***

## Unix Name

This command provides a comprehensive overview of your system's kernel and hardware, including:

* Kernel name: (e.g., Linux)
* Network node hostname: The name of your machine on the network.
* Kernel release: The specific version of your kernel (e.g., 5.15.0-76-generic).
* Kernel version: More detailed information about the kernel build, including the compile date and compiler used.
* Machine hardware name: The architecture of your hardware (e.g., x86\_64).
* Processor type: (e.g., x86\_64)
* Hardware platform: (e.g., x86\_64)
* Operating system: (e.g., GNU/Linux)

{% code title="Command" %}
```bash
uname -a
```
{% endcode %}

`uname`: This command (short for "Unix name") prints system information.

`-a`: This option tells `uname` to print all available information.

{% code title="Example Output" overflow="wrap" %}
```bash
Linux my-machine 5.15.0-76-generic #83-Ubuntu SMP Mon Jun 19 16:32:04 UTC 2023 x86_64 x86_64 x86_64 GNU/Linux
```
{% endcode %}

***

## Proc File System

This file contains a string that identifies the version of the Linux kernel currently running, including the GCC version used to compile it and the time of compilation. It's often very similar to the "Kernel version" part of `uname -a`.

{% code title="Command" %}
```bash
cat /proc/version
```
{% endcode %}

`/proc/version`: This is a virtual file within the `/proc` filesystem. The `/proc` filesystem is a pseudo-filesystem that provides an interface to kernel data structures.

{% code title="Example Output" overflow="wrap" %}
```bash
Linux version 5.15.0-76-generic (buildd@lcy02-amd64-060) (gcc (Ubuntu 11.3.0-1ubuntu1~22.04.1) 11.3.0, GNU ld (GNU Binutils for Ubuntu) 2.38) #83-Ubuntu SMP Mon Jun 19 16:32:04 UTC 2023
```
{% endcode %}

***

## Operating System Identification

This file typically displays the operating system's name and version, and sometimes additional information like the architecture. It's a quick way to see what Linux distribution is running.

{% code title="Command" %}
```bash
cat /etc/issue
```
{% endcode %}

`/etc/issue`: This file contains a system identification string that is displayed _before_ the login prompt. It's often used by distributions to show their name and version.

{% code title="Ubuntu Output" %}
```bash
Ubuntu 22.04.2 LTS \n \l
```
{% endcode %}

<pre class="language-bash" data-title="CentOS/RHEL Output"><code class="lang-bash"><strong>CentOS Linux release 8.5.2111 (Core)
</strong>Kernel \r on an \m
</code></pre>

***

## Login Shells

{% code title="Command" %}
```bash
cat /etc/shells
```
{% endcode %}

{% code title="Example Output" %}
```bash
# /etc/shells: valid login shells
/bin/sh
/bin/bash
/usr/bin/bash
/bin/rbash
/usr/bin/rbash
/bin/dash
/usr/bin/dash
/usr/bin/tmux
/usr/bin/screen
```
{% endcode %}

***

## File System & Unmounted Drives

### Discovering Drives

If we discover - and can mount - an additional drive or unmounted file system, we may find sensitive files, passwords, or backups that can be leveraged to escalate privileges.

{% code title="Command" %}
```bash
lsblk
```
{% endcode %}

{% code title="Example Output" %}
```bash
NAME   MAJ:MIN RM  SIZE RO TYPE MOUNTPOINT
sda      8:0    0   30G  0 disk 
├─sda1   8:1    0   29G  0 part /
├─sda2   8:2    0    1K  0 part 
└─sda5   8:5    0  975M  0 part [SWAP]
sr0     11:0    1  848M  0 rom  
```
{% endcode %}

### Protected Drive Passwords

`/etc/fstab` may contain credentials for protect drives. We can attempt to find these credentials - or where they're stored by grepping for common words such as password, username, credential, etc in `/etc/fstab`.

{% code title="Command" %}
```bash
cat /etc/fstab | grep -E 'credential|password|username'
```
{% endcode %}

{% code title="Example Output: Network share with credentials (common)" %}
```bash
//192.168.1.10/share /mnt/share cifs credentials=/home/user/.smbcreds 0 0
```
{% endcode %}

{% code title="Example Output: Credentials stored directly (uncommon but possible)" %}
```bash
//192.168.1.11/backup /mnt/backup cifs rw,user,username=backupuser,password=secretpass 0 0
```
{% endcode %}

***

## Discover Printers

The command `lpstat` can be used to find information about any printers attached to the system. If there are active or queued print jobs we can attempt to gain access and maybe find some sensitive information.

{% code title="Command" %}
```bash
lpstat
```
{% endcode %}

{% code title="Example Output (if jobs are queued)" %}
```bash
LaserJet_P2-14  jdoe  81920   Sep 26 10:35
PDF_Printer-15  jdoe  15360   Sep 26 12:05
```
{% endcode %}

| Command   | Purpose | Example Output |
| --------- | ------- | -------------- |
| lpstat -d |         |                |
| lpstat -p |         |                |
|           |         |                |
|           |         |                |
