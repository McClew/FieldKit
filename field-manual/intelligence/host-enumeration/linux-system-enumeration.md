---
icon: linux
---

# Linux System Enumeration

## Tools

Scripts such as [LinEnum](https://github.com/rebootuser/LinEnum) can help automate these processes.

***

## Unix Name

```bash
uname -a
```

`uname`: This command (short for "Unix name") prints system information.

`-a`: This option tells `uname` to print all available information.

### What it Tells You

This command provides a comprehensive overview of your system's kernel and hardware, including:

* Kernel name: (e.g., Linux)
* Network node hostname: The name of your machine on the network.
* Kernel release: The specific version of your kernel (e.g., 5.15.0-76-generic).
* Kernel version: More detailed information about the kernel build, including the compile date and compiler used.
* Machine hardware name: The architecture of your hardware (e.g., x86\_64).
* Processor type: (e.g., x86\_64)
* Hardware platform: (e.g., x86\_64)
* Operating system: (e.g., GNU/Linux)

### Example Output

{% code overflow="wrap" %}
```bash
Linux my-machine 5.15.0-76-generic #83-Ubuntu SMP Mon Jun 19 16:32:04 UTC 2023 x86_64 x86_64 x86_64 GNU/Linux
```
{% endcode %}

***

## Proc File System

```bash
cat /proc/version
```

`/proc/version`: This is a virtual file within the `/proc` filesystem. The `/proc` filesystem is a pseudo-filesystem that provides an interface to kernel data structures.

### What it Tells You:

This file contains a string that identifies the version of the Linux kernel currently running, including the GCC version used to compile it and the time of compilation. It's often very similar to the "Kernel version" part of `uname -a`.

### Example Output

{% code overflow="wrap" %}
```bash
Linux version 5.15.0-76-generic (buildd@lcy02-amd64-060) (gcc (Ubuntu 11.3.0-1ubuntu1~22.04.1) 11.3.0, GNU ld (GNU Binutils for Ubuntu) 2.38) #83-Ubuntu SMP Mon Jun 19 16:32:04 UTC 2023
```
{% endcode %}

***

## Operating System Identification

```bash
cat /etc/issue
```

`/etc/issue`: This file contains a system identification string that is displayed _before_ the login prompt. It's often used by distributions to show their name and version.

### What it shows you

This file typically displays the operating system's name and version, and sometimes additional information like the architecture. It's a quick way to see what Linux distribution is running.

### Example Output

{% code title="Ubuntu Output" %}
```bash
Ubuntu 22.04.2 LTS \n \l
```
{% endcode %}

<pre class="language-bash" data-title="CentOS/RHEL Output"><code class="lang-bash"><strong>CentOS Linux release 8.5.2111 (Core)
</strong>Kernel \r on an \m
</code></pre>
