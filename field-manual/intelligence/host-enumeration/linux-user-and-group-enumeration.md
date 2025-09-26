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

# Linux User & Group Enumeration

## Current User

### sudo Privileges

Can the user run any commands either as another user or as root? If you do not have credentials for the user, it may not be possible to leverage sudo permissions.&#x20;

{% hint style="success" %}
Often sudoer entries include `NOPASSWD`, meaning that the user can run the specified command without being prompted for a password.
{% endhint %}

Not all commands, even we can run as root, will lead to privilege escalation. It is not uncommon to gain access as a user with full sudo privileges, meaning they can run any command as root. Issuing a simple `sudo su` command will immediately give you a root session.

```bash
sudo -l
```

```bash
Matching Defaults entries for sysadm on NIX02:
    env_reset, mail_badpass, secure_path=/usr/local/sbin\:/usr/local/bin\:/usr/sbin\:/usr/bin\:/sbin\:/bin\:/snap/bin

User sysadm may run the following commands on NIX02:
    (root) NOPASSWD: /usr/sbin/tcpdump
```

***

### History

It can be important to check a user's bash history, as they may be passing passwords as an argument on the command line, working with git repositories, setting up cron jobs, and more. Reviewing what the user has been doing can give you considerable insight into the type of server you land on and give a hint as to privilege escalation paths.

{% code title="Command" %}
```bash
history
```
{% endcode %}

{% code title="Example Output" %}
```bash
    1  id
    2  cd /home/cliff.moore
    3  exit
    4  touch backup.sh
    5  tail /var/log/apache2/error.log
    6  ssh ec2-user@dmz02.inlanefreight.local
    7  history
```
{% endcode %}

***

### SSH Directory

If we find an SSH key for our current user, this could be used to open an SSH session on the host (if SSH is exposed externally) and gain a stable and fully interactive session. SSH keys could be leveraged to access other systems within the network as well. At the minimum, check the ARP cache to see what other hosts are being accessed and cross-reference these against any useable SSH private keys.

{% code title="Command" %}
```bash
ls -l ~/.ssh
```
{% endcode %}

{% code title="Example Output" %}
```bash
total 8
-rw------- 1 mrb3n mrb3n 1679 Aug 30 23:37 id_rsa
-rw-r--r-- 1 mrb3n mrb3n  393 Aug 30 23:37 id_rsa.pub
```
{% endcode %}

***

### $PATH Variable

We should check out our current user's PATH, which is where the Linux system looks every time a command is executed for any executables to match the name of what we type, i.e., `id` which on this system is located at `/usr/bin/id`.

As we'll see later in this module, if the PATH variable for a target user is misconfigured we may be able to leverage it to escalate privileges. For now we'll note it down and add it to our note-taking tool of choice.

{% code title="Command" %}
```bash
echo $PATH
```
{% endcode %}

{% code title="Example Output" %}
```bash
/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin:/usr/games:/usr/local/games:/snap/bin
```
{% endcode %}

***

### Environment Variables

We can also check out all environment variables that are set for our current user, we may get lucky and find something sensitive in there such as a password.

{% code title="Command" %}
```bash
env
```
{% endcode %}

{% code title="Example Output" %}
```bash
SHELL=/bin/bash
PWD=/home/htb-student
LOGNAME=htb-student
XDG_SESSION_TYPE=tty
MOTD_SHOWN=pam
HOME=/home/htb-student
LANG=en_US.UTF-8
<SNIP>
```
{% endcode %}

***

### SETUID & SETGID Permissions

Binaries are set with these permissions to allow a user to run a command as root, without having to grant root-level access to the user. Many binaries contain functionality that can be exploited to get a root shell.

#### 1. Using Find

The most comprehensive and standard command to search the entire file system for files with either SUID or SGID bits set is:

{% code title="Command" %}
```bash
find / -type f -perm /6000 2>/dev/null
```
{% endcode %}

`find /` Start the search from the root directory (`/`).

`-type f` Only look for files (executables), not directories.

`-perm /6000` Search for files where _ANY_ of the following bits are set:

* 4000 (SUID - Set User ID)
* 2000 (SGID - Set Group ID)
* The `/` operator acts as an OR, matching files with _either_ bit set.

`2>/dev/null` Suppress permission denied and other error messages for a cleaner output.

You can also search for the permissions individually:

Only SUID (Set User ID): `find / -type f -perm /4000 2>/dev/null4000`

Only SGID (Set Group ID): `find / -type f -perm /2000 2>/dev/null2000`

#### 2. Analysing the Output with `ls -l`

Once we have a list of files, use `ls -l` on them to confirm the special permission.

{% code title="Example SUID Output: Runs as the owner (root)" %}
```
-rwsr-xr-x 1 root root ... /bin/passwd
```
{% endcode %}

{% code title="Example SGID Output: Runs with the privileges of the group (tty)" %}
```bash
-rwxr-sr-x 1 root tty ... /usr/bin/wall
```
{% endcode %}

#### 3. Post-Enumeration Steps

The mere existence of a SUID/SGID binary is not a vulnerability, the next step is to check the binaries against a known database of exploits:

1. Check GTFObins: This is the most crucial step. Use the list of SUID/SGID binaries you found and search for them on the [GTFObins](https://gtfobins.github.io/) website. This resource documents known ways to abuse Linux binaries (often with SUID set) to gain a shell, read/write files, or bypass security restrictions.
2. Look for Suspect Files: Pay extra attention to SUID binaries owned by `root` that are not standard system utilities (like `passwd`, `su`, or `mount`). Custom-compiled programs in unusual directories are prime targets.
3. Check Path Variable/Relative Execution: Look for SUID/SGID binaries that call other programs using a relative path (e.g., calling `ls` instead of `/bin/ls`). If we can inject our own malicious version of that program into a directory that is checked earlier in the `$PATH` environment variable, we can hijack the execution and gain the elevated privileges.

***

### Writable Directories

It is important to discover which directories are writeable if we need to download tools to the system. We may discover a writeable directory where a cron job places files, which provides an idea of how often the cron job runs and could be used to elevate privileges if the script that the cron job runs is also writeable.

#### Finding Writable Directories

{% code title="Command" %}
```bash
find / -path /proc -prune -o -type d -perm -o+w 2>/dev/null
```
{% endcode %}

{% code title="Example Output" %}
```bash
/dmz-backups
/tmp
/tmp/VMwareDnD
/tmp/.XIM-unix
/tmp/.Test-unix
/tmp/.X11-unix
/tmp/systemd-private-8a2c51fcbad240d09578916b47b0bb17-systemd-timesyncd.service-TIecv0/tmp
/tmp/.font-unix
/tmp/.ICE-unix
/proc
/dev/mqueue
/dev/shm
/var/tmp
/var/tmp/systemd-private-8a2c51fcbad240d09578916b47b0bb17-systemd-timesyncd.service-hm6Qdl/tmp
/var/crash
/run/lock

```
{% endcode %}

***

### Writeable Files

Are any scripts or configuration files globally writable? While altering configuration files can be extremely destructive, there may be instances where a minor modification can open up further access. Also, any scripts that are run as root using cron jobs can be modified slightly to append a command.

#### Finding Writable Files

{% code title="Command" %}
```bash
find / -path /proc -prune -o -type f -perm -o+w 2>/dev/null
```
{% endcode %}

{% code title="Example Output" %}
```bash
/etc/cron.daily/backup
/dmz-backups/backup.sh
/proc
/sys/fs/cgroup/memory/init.scope/cgroup.event_control
<SNIP>
/home/backupsvc/backup.sh
<SNIP>
```
{% endcode %}

***

## User Accounts

### Hunting for Usernames

An easy way for us to find other users is from the `/etc/passwd` file. This file is readable by all users and should provide all of the required information:

{% code title="Command" %}
```bash
cat /etc/passwd
```
{% endcode %}

{% code title="Example Output" %}
```bash
root:x:0:0:root:/root:/bin/bash
daemon:x:1:1:daemon:/usr/sbin:/usr/sbin/nologin
bin:x:2:2:bin:/bin:/usr/sbin/nologin
sys:x:3:3:sys:/dev:/usr/sbin/nologin
sync:x:4:65534:sync:/bin:/bin/sync
games:x:5:60:games:/usr/games:/usr/sbin/nologin
man:x:6:12:man:/var/cache/man:/usr/sbin/nologin
lp:x:7:7:lp:/var/spool/lpd:/usr/sbin/nologin
mail:x:8:8:mail:/var/mail:/usr/sbin/nologin
news:x:9:9:news:/var/spool/news:/usr/sbin/nologin
<...SNIP...>
dnsmasq:x:109:65534:dnsmasq,,,:/var/lib/misc:/bin/false
sshd:x:110:65534::/var/run/sshd:/usr/sbin/nologin
mrb3n:x:1000:1000:mrb3n,,,:/home/mrb3n:/bin/bash
colord:x:111:118:colord colour management daemon,,,:/var/lib/colord:/bin/false
backupsvc:x:1001:1001::/home/backupsvc:
bob.jones:x:1002:1002::/home/bob.jones:
cliff.moore:x:1003:1003::/home/cliff.moore:
logger:x:1004:1004::/home/logger:
shared:x:1005:1005::/home/shared:
stacey.jenkins:x:1006:1006::/home/stacey.jenkins:
sysadm:$6$vdH7vuQIv6anIBWg$Ysk.UZzI7WxYUBYt8WRIWF0EzWlksOElDE0HLYinee38QI1A.0HW7WZCrUhZ9wwDz13bPpkTjNuRoUGYhwFE11:1007:1007::/home/sysadm:
```
{% endcode %}

***

## Groups

***

## Password Enumeration

### /etc/passwd

Occasionally, we may see password hashes directly in the `/etc/passwd` file. This file is readable by all users, and as with hashes in the `shadow` file, these can be subjected to an offline password cracking attack. This configuration, while not common, can sometimes be seen on embedded devices and routers.

### /etc/shadow

If the shadow file is readable, we will be able to gather password hashes for all users who have a password set. While this does not guarantee further access, these hashes can be subjected to an offline brute-force attack to recover the cleartext password.
