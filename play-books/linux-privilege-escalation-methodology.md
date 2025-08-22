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

# Linux Privilege Escalation Methodology

## Webserver Methodology

1. Check web configuration files and source code for vulnerabilities, hard coded credentials, etc.
   * Credential hunting.
   * Check the source code.
   * Apache ( httpd ):
     * /var/www/html/ - default root for Apache on most distros.
     * /var/www/- base directory for mutliple sites or vhosts.
     * /srv/http/ - default on Arch.
     * /usr/share/httpd/ - sometimes used on CentOS / RHEL.
     * /etc/apache2/sites-available/ - default site config location.
     * /etc/apache2/sites-enabled/ - default site config location.
   * Nginx:
     * /usr/share/nginx/html/ - default on CentOS / RHEL.
     * /var/www/html/ - common if configured similarly to Apache.
     * /etc/nginx/sites-available/ - default site config location.
     * /etc/nginx/sites-enabled/ - default site config location.
   * Other possible locations:
     * /opt/web/ - custom installations.
     * /home/\[user]/public\_html/ - sometimes used for user web directories.
     * /www/ or /html/ - user specific web roots.

## Default Methodology

1. Run Linux privilege escalation automation scripts. Save output to a file and transfer it back to host for examination.
   * LinPeas
   * LinEnum
2. Perform basuc enumeration once a foothold is established.
   * Enumerate current user.
   * Check sudo privileges.
   * Check OS/Kernel version.
   * Check $PATH variable.
   * List processes running as root.
   * Enumerate Users.
   * Check for SSH Keys.
   * Check current user bash history.
   * Check if Shadow is reable or Passwd is writeable.
     * Check for hashes iun /etc/passwd.
   * Enumerate Groups.
   * Check Cron jobs.
   * Check for unmounted files systems and additional drives.
     * Weak NFS privileges.
   * Find writable directories and files.
   * Enumerate all hidden files and directories.
   * Enumerate services and binaries.
   * Check for accessible configuration files.
   * Check for accessible scripts.
3. Enumerate for hardcoded/cleartext credentials on the system.
   * Be attentive to out of the ordinary services.
   * Look for artefacts in /opt.
   * Look for web config files.
   * Credential hunting.
4. Look at the access rights of the foothold user with Sudo, SUID, GUID.
   * Sudo privileges
   * SUID / GUID privileges.
   * Check for LOTL vulnerabilities using [GTFOBins](https://gtfobins.github.io/).
5. Check for unique files owned by the user or by the users group.
6. If the user has sudo privileges over a binary not in [GTFOBins](https://gtfobins.github.io/), try LD PRELOAD exploit.
7. If custom binaries exist with the SETUID bit set, try Shared Object hacking.
8. Check the groups the user is a part of and see if any of them are privileged groups.
   * Enumerate existing groups.
   * LXC/LXD Group membership.
   * Docker Group membership.
   * Disk Group Membership.
   * ADM Group membership.
9. Check for PATH Abuse (unlikely).
10. Check for Wildcard abuse in cron jobs or custom scripts.
11. Look for services running on internal ports that were not accessible externally with netstat.
    * Internal listening ports/services.
12. Check for additional NICs using commands like `ifconfig` to see if there are any other sub-networks.
    * Enumerate network interfaces
    * enumerate other hostnames.
13. Look for cronjobs running writeable scripts, services running as root or another privileged user.
14. Look for abusable linux capabilities.
15. Look for vulnerable applicaiton/service versions.
16. Checl for Logrotate exploit.
17. Check for Kernel exploits.
18. If Python script exists, check for Python library hijacking.
19. Check for vulnerable version of Netfilter.
20. Check for hijackable tmux sessions.
21. If tcpdump exists on the machine, try capturing cleartext traffic for credentials.
22. Check for recent exploits and zero days.
23. Attempt to brute force root user with password file.
