---
icon: arrow-progress
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

# Network Enumeration Methodology

## Host Enumeration

[host-enumeration](../field-manual/intelligence/host-enumeration/ "mention")

Identify active hosts on the network.

* [ ] Identify the target IP address or range.
* [ ] Perform a simple host discovery scan to find live hosts.
* [ ] Determine if any firewalls are in place.

## Port & Service Enumeration

[port-and-service-enumeration](../field-manual/intelligence/port-and-service-enumeration/ "mention")

Once a live host has been identified, search for open ports and the services running on them. **Find a point of entry.**

* [ ] Perform a full TCP port scan (-p-).
* [ ] Perform a UDP port scan (-sU). Don't forget this; it's a common oversight.
* [ ] Use service and version detection (-sV).
* [ ] Use default Nmap scripts to gather more info (-sC).
* [ ] Save the scan results for future reference (-oA).

## Service-level Enumeration

Further investigate every open port and service, this is where key weaknesses can be found.

### FTP

[21-ftp.md](../field-manual/intelligence/port-and-service-enumeration/21-ftp.md "mention")

[22-sftp.md](../field-manual/intelligence/port-and-service-enumeration/22-sftp.md "mention")

* [ ] Check for anonymous login.
* [ ] Try to list files in the root directory.
* [ ] Look for writable directories or configuration files.

### SSH

[22-ssh.md](../field-manual/intelligence/port-and-service-enumeration/22-ssh.md "mention")

* [ ] Check for allowed authentication methods.
* [ ] Brute-force common usernames and passwords.

### DNS

[53-dns.md](../field-manual/intelligence/port-and-service-enumeration/53-dns.md "mention")

* [ ] Attempt zone transfers.
* [ ] Enumerate subdomains.

### HTTP/S

[80-443-web.md](../field-manual/intelligence/port-and-service-enumeration/80-443-web.md "mention")

* [ ] Browse the website. Look for robots.txt, sitemap.xml, comments in the source code.
* [ ] Run a directory and file bruteforcing tool.
* [ ] Check for common virtual hosts and subdomains.
* [ ] Analyze the HTTP headers for juicy information (e.g., server versions, X-Powered-By).
* [ ] Use a web application scanner.
* [ ] Check for common vulnerabilities (LFI, RFI, SQLi, XSS).

### SMB/Samba

[139-445-smb.md](../field-manual/intelligence/port-and-service-enumeration/139-445-smb.md "mention")

* [ ] Check for anonymous access.
* [ ] Enumerate users, groups, and shares.
* [ ] Check for Null sessions.
* [ ] Look for writable shares or sensitive information.
