---
icon: linux
layout:
  width: default
  title:
    visible: true
  description:
    visible: false
  tableOfContents:
    visible: true
  outline:
    visible: true
  pagination:
    visible: false
  metadata:
    visible: true
---

# Linux Enumeration

## Internal Enumeration

\-

### System & Kernel

[intelligence](../field-manual/intelligence/ "mention") > [host-enumeration](../field-manual/intelligence/host-enumeration/ "mention") > [linux-system-enumeration.md](../field-manual/intelligence/host-enumeration/linux-system-enumeration.md "mention")

{% stepper %}
{% step %}
* #### System Identification

Gather the basic operating system details, distribution version, and kernel release to identify known vulnerabilities (e.g., DirtyPipe, PwnKit).

Commands:&#x20;

```bash
uname -a
```

```bash
cat /etc/os-release
```

```bash
hostnamectl
```
{% endstep %}

{% step %}
* #### Kernel Exploitation Check

Compare the kernel version against exploit databases like [searchsploit.md](../toolbox/tooling/exploitation-tools/searchsploit.md "mention") or `Exploit-DB`. Identify if the kernel is unpatched against public local privilege escalation (LPE) exploits.
{% endstep %}

{% step %}
* #### Environment Variables

Review the `$PATH` for writable directories and check for sensitive information (API keys, secrets) stored in environment variables.

Command:

```bash
env
```

```bash
printenv
```
{% endstep %}
{% endstepper %}

### User & Group

[intelligence](../field-manual/intelligence/ "mention") > [host-enumeration](../field-manual/intelligence/host-enumeration/ "mention") > [linux-user-and-group-enumeration.md](../field-manual/intelligence/host-enumeration/linux-user-and-group-enumeration.md "mention")

{% stepper %}
{% step %}
* #### Current User Context

Determine our current privileges, group memberships, and special capabilities.

Commands:

```bash
id
```

```bash
whoami
```

```bash
getcap -r / 2>/dev/null
```
{% endstep %}

{% step %}
* #### User List & Login History

Identify other human users on the system and see who has logged in recently to find high-value targets.

Commands:&#x20;

```bash
cat /etc/passwd | cut -d: -f1
```

```bash
last
```

```bash
w
```
{% endstep %}

{% step %}
* #### Sudo Permissions

Check if the current user can execute commands as root or another user, specifically looking for `NOPASSWD` entries.

Commands:

```bash
sudo -l
```
{% endstep %}
{% endstepper %}

### Network

[intelligence](../field-manual/intelligence/ "mention") > [host-enumeration](../field-manual/intelligence/host-enumeration/ "mention") > [linux-network-enumeration.md](../field-manual/intelligence/host-enumeration/linux-network-enumeration.md "mention")

{% stepper %}
{% step %}
* #### Internal Network Connections

Identify services running locally (127.0.0.1) that were not visible during your external Nmap scan.

Commands:

```bash
netstat -ano
```

```bash
ss -tulnp
```
{% endstep %}
{% endstepper %}

### Program & Process

{% stepper %}
{% step %}
* #### Running Processes & Services

Look for processes running as `root` or other users, especially custom binaries or non-standard services.

Commands:

```bash
ps aux
```

```bash
top
```
{% endstep %}

{% step %}
* #### Cron Jobs & Scheduled Tasks

Examine system-wide and user-specific crontabs for scripts that run with elevated privileges.

Commands:

```bash
cat /etc/crontab
```

```bash
ls -la /etc/cron.*
```
{% endstep %}
{% endstepper %}

### File System & Sensitive Data

{% stepper %}
{% step %}
* #### SUID/SGID Binaries

Locate files with the SUID bit set, which execute with the privileges of the file owner (often root).

Commands:

```bash
find / -perm -u=s -type f 2>/dev/null
```
{% endstep %}

{% step %}
* #### Writable Files & Directories

Search for configuration files or system scripts that the current user has write access to.

Commands:

```bash
find / -writable -type f 2>/dev/null | grep -v "/proc"
```
{% endstep %}

{% step %}
* #### SSH Key & Config Harvesting

Check home directories for hidden SSH keys, known\_hosts, or configuration files containing credentials.

Commands:

```bash
ls -la ~/.ssh/
```

```bash
cat /etc/ssh/sshd_config
```
{% endstep %}
{% endstepper %}
