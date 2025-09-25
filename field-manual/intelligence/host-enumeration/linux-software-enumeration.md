---
icon: linux
---

# Linux Software Enumeration

## Running Processes

`ps aux` provides a snapshot of all processes running on your system, presenting them in a comprehensive, user-friendly format. The most important columns include:

* USER: The effective user ID of the process owner.
* PID: The Process ID.
* TTY: The controlling terminal for the process. `?` means no controlling terminal.
* STAT: The process status (e.g., `R` for running, `S` for sleeping, `Z` for zombie, `T` for stopped).
* COMMAND: The command that started the process, including its arguments.

{% code title="Command" %}
```bash
ps aux
```
{% endcode %}

`ps` (process status): This command reports information about currently running processes.

`a` (all): This option selects all processes on a terminal, including those of other users.

`u` (user-oriented format): This option displays processes in a user-oriented format, which includes details like the user, CPU and memory usage, start time, and command.

`x` (processes not attached to a terminal) This option selects processes not associated with a terminal. When combined with `a`, it shows almost all processes running on the system.

{% code title="Example Output" %}
```bash
USER         PID %CPU %MEM    VSZ   RSS TTY      STAT START   TIME COMMAND
root           1  0.0  0.0 168432 12104 ?        Ss   Jul05   0:02 /sbin/init
root           2  0.0  0.0      0     0 ?        S    Jul05   0:00 [kthreadd]
root           3  0.0  0.0      0     0 ?        I<   Jul05   0:00 [rcu_gp]
<snip>
myuser     12345  0.5  2.1 123456 54321 pts/0    S+   10:00   0:01 /usr/bin/python3 myapp.py
```
{% endcode %}

***

## Cron Jobs

Cron jobs on Linux systems are similar to Windows scheduled tasks. They are often set up to perform maintenance and backup tasks. In conjunction with other misconfigurations such as relative paths or weak permissions, they can leverage to escalate privileges when the scheduled cron job runs.

{% code title="Command" %}
```bash
ls -la /etc/cron.daily/
```
{% endcode %}

{% code title="Example Output" %}
```bash
total 60
drwxr-xr-x  2 root root 4096 Aug 30 23:49 .
drwxr-xr-x 93 root root 4096 Aug 30 23:47 ..
-rwxr-xr-x  1 root root  376 Mar 31  2016 apport
-rwxr-xr-x  1 root root 1474 Sep 26  2017 apt-compat
-rwx--x--x  1 root root  379 Aug 30 23:49 backup
-rwxr-xr-x  1 root root  355 May 22  2012 bsdmainutils
-rwxr-xr-x  1 root root 1597 Nov 27  2015 dpkg
-rwxr-xr-x  1 root root  372 May  6  2015 logrotate
-rwxr-xr-x  1 root root 1293 Nov  6  2015 man-db
-rwxr-xr-x  1 root root  539 Jul 16  2014 mdadm
-rwxr-xr-x  1 root root  435 Nov 18  2014 mlocate
-rwxr-xr-x  1 root root  249 Nov 12  2015 passwd
-rw-r--r--  1 root root  102 Apr  5  2016 .placeholder
-rwxr-xr-x  1 root root 3449 Feb 26  2016 popularity-contest
-rwxr-xr-x  1 root root  214 May 24  2016 update-notifier-common
```
{% endcode %}

