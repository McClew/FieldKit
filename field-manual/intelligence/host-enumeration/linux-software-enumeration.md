---
icon: linux
---

# Linux Software Enumeration

## Process Status

```bash
ps aux
```

`ps` (process status): This command reports information about currently running processes.

`a` (all): This option selects all processes on a terminal, including those of other users.

`u` (user-oriented format): This option displays processes in a user-oriented format, which includes details like the user, CPU and memory usage, start time, and command.

`x` (processes not attached to a terminal) This option selects processes not associated with a terminal. When combined with `a`, it shows almost all processes running on the system.

### What it Tells You

`ps aux` provides a snapshot of all processes running on your system, presenting them in a comprehensive, user-friendly format. The most important columns include:

* USER: The effective user ID of the process owner.
* PID: The Process ID.
* TTY: The controlling terminal for the process. `?` means no controlling terminal.
* STAT: The process status (e.g., `R` for running, `S` for sleeping, `Z` for zombie, `T` for stopped).
* COMMAND: The command that started the process, including its arguments.

### Example Output

```bash
USER         PID %CPU %MEM    VSZ   RSS TTY      STAT START   TIME COMMAND
root           1  0.0  0.0 168432 12104 ?        Ss   Jul05   0:02 /sbin/init
root           2  0.0  0.0      0     0 ?        S    Jul05   0:00 [kthreadd]
root           3  0.0  0.0      0     0 ?        I<   Jul05   0:00 [rcu_gp]
<snip>
myuser     12345  0.5  2.1 123456 54321 pts/0    S+   10:00   0:01 /usr/bin/python3 myapp.py
```
