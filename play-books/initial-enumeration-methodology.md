---
icon: flag-checkered
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

# Initial Enumeration Methodology

## Passive Recon

1. Gather public info.\
   ( [dns.md](../field-manual/intelligence/network-enumeration/dns.md "mention"), [whois.md](../field-manual/intelligence/web-enumeration/whois.md "mention"), certificates, company structure )
2. Search public data leaks.\
   ( [search-engine-discovery.md](../field-manual/intelligence/osint/search-engine-discovery.md "mention"), GitHub, Pastebin )

## Active Recon

1. Discover all active hosts on the target network/IP range/subnet(s). Document all active hosts.
   * Host discovery scan.\
     ( [host-discovery.md](../toolbox/tooling/information-gathering/nmap/host-discovery.md "mention"), [nmap](../toolbox/tooling/information-gathering/nmap/ "mention") )
   * ICMP sweep.\
     ( ping or fping )
   * TCP/UDP host discovery.\
     ( nmap -sn, masscan )
   * ARP scanning - if on same subnet.
   * Add discovered hostnames to **/etc/hosts** file.
2. For each active host, scan all TCP/UDP ports. Document each open port for the respective host.
3. For each open port, run service version scan on discovered ports with scripts and OS detection.
   * [nmap](../toolbox/tooling/information-gathering/nmap/ "mention") service enumeration & OS detection.
   * [netcat.md](../toolbox/tooling/post-exploitation/netcat.md "mention") [banner-grabbing.md](../field-manual/intelligence/port-and-service-enumeration/banner-grabbing.md "mention") for manual confirmation.
   * Document services and service versions.
4. For each detected service, conduct [service-enumeration.md](../toolbox/tooling/information-gathering/nmap/service-enumeration.md "mention") to find more information and potential vulnerabilities.
5. Check for vulnerabilities in discovered services and service versions.
   * Search [metasploit](../toolbox/tooling/exploitation-tools/metasploit/ "mention") for exploits.
   * Search [ExploitDB](https://www.exploit-db.com/) for exploits.
   * Look at [nmap](../toolbox/tooling/information-gathering/nmap/ "mention") script output for discovered vulnerabilities or misconfigurations.
   * Look for OS version exploits.
   * Document discovered vulnerabilities.
6. Check file share services for for anonymous logon and credential files.
   * [21-ftp.md](../field-manual/intelligence/port-and-service-enumeration/21-ftp.md "mention") enumeration.
   * [139-445-smb.md](../field-manual/intelligence/port-and-service-enumeration/139-445-smb.md "mention") enumeration.
