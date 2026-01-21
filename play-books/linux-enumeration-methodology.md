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

# Linux Enumeration Methodology

### System & Kernel Information

{% stepper %}
{% step %}
* #### System Identification

[intelligence](../field-manual/intelligence/ "mention") > [host-enumeration](../field-manual/intelligence/host-enumeration/ "mention") > [linux-system-enumeration.md](../field-manual/intelligence/host-enumeration/linux-system-enumeration.md "mention")

Gather the basic operating system details, distribution version, and kernel release to identify known vulnerabilities (e.g., DirtyPipe, PwnKit).

Commands:&#x20;

* `uname -a`
* `cat /etc/os-release`
* `hostnamectl`
{% endstep %}

{% step %}
* #### Kernel Exploitation Check

Compare the kernel version against exploit databases like [searchsploit.md](../toolbox/tooling/exploitation-tools/searchsploit.md "mention") or `Exploit-DB`. Identify if the kernel is unpatched against public local privilege escalation (LPE) exploits.
{% endstep %}

{% step %}
* #### Environment Variables

Review the `$PATH` for writable directories and check for sensitive information (API keys, secrets) stored in environment variables.

* Command: `env`, `printenv`.
{% endstep %}
{% endstepper %}

### Public-Facing Assets and Code

{% stepper %}
{% step %}
* #### Search Engine Dorking

Use [search-engine-discovery.md](../field-manual/intelligence/osint/search-engine-discovery.md "mention") techniques, advanced search operators (Google Dorks, Bing Dorks) to find sensitive files, error messages, login pages, or exposed directories.
{% endstep %}

{% step %}
* #### Code Repositories

Search platforms like GitHub, GitLab, and Bitbucket for exposed credentials, API keys, configuration files, or internal documentation related to the target.
{% endstep %}

{% step %}
* #### Public File Metadata

Analyse document metadata (PDFs, DOCX) found online for internal usernames, server names, or geographical data.
{% endstep %}
{% endstepper %}

### Employee & Organisation Data

{% stepper %}
{% step %}
* #### Social Media Analysis

Search for key employees on platforms like LinkedIn to understand organisational structure, technology stacks, and potential phishing targets.
{% endstep %}

{% step %}
* #### Email Harvesting

Attempt to determine a common email format for the organisation (e.g., `firstname.lastname@target.com`).
{% endstep %}
{% endstepper %}

***

## Active Reconnaissance

Active reconnaissance involves direct interaction with the target's network, which increases the likelihood of detection but provides much richer data.

### Host Discovery and Port Scanning

{% stepper %}
{% step %}
* #### Ping Sweeps/ICMP

Identify which hosts are alive on the target network.
{% endstep %}

{% step %}
* #### TCP/UDP Port Scanning (Initial)

Use a tool like [nmap](../toolbox/tooling/information-gathering/network-enumeration/nmap/ "mention") to identify common open ports and the services running on them. Start with less-invasive, non-aggressive scans.
{% endstep %}

{% step %}
* #### Firewall/IDS Evasion

Test methods to bypass basic network defenses.

_Potential Link: Tools & Techniques: Nmap Evasion_
{% endstep %}
{% endstepper %}

### Service and Version Enumeration

{% stepper %}
{% step %}
* #### Deep Service Scanning

Re-scan the open ports identified in the initial step to accurately determine the software version, operating system, and specific configurations (e.g., HTTP headers, SSL/TLS certificate details). Ports & services can be scanned using tools like [nmap](../toolbox/tooling/information-gathering/network-enumeration/nmap/ "mention").
{% endstep %}

{% step %}
* #### Web Server Enumeration

Access identified web services to check for common files like `robots.txt`, `sitemap.xml`, or hidden directories. See [#robots.txt](../field-manual/intelligence/web-enumeration/crawling-spidering.md#robots.txt "mention") for more information.&#x20;
{% endstep %}
{% endstepper %}

### Directory and File Enumeration (For Web Targets)

{% stepper %}
{% step %}
* #### Content Discovery (Fuzzing/Brute-forcing)

Use tools like [ffuf.md](../toolbox/tooling/web-application-analysis/ffuf.md "mention") or [gobuster.md](../toolbox/tooling/information-gathering/gobuster.md "mention") against identified web services to find hidden files, folders, or pages that are not linked from the main site.
{% endstep %}

{% step %}
* #### Virtual Host Enumeration

Check for multiple websites hosted on the same IP address.
{% endstep %}
{% endstepper %}
