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

## Passive Reconnaissance

Passive reconnaissance involves gathering information without directly interacting with the target's systems, minimising detection risk.

### Domain and Host Information

{% stepper %}
{% step %}
* #### WHOIS Lookup

Retrieve registration data, including creation/expiration dates, registrar, and potentially contact information (if not privacy-protected).
{% endstep %}

{% step %}
* #### DNS Record Examination

Use tools to query A, AAAA, MX, NS, and TXT records to map the infrastructure.
{% endstep %}

{% step %}
* #### Subdomain Enumeration

Use techniques (e.g., certificate transparency logs, brute-forcing, passive lookups) to find hidden or forgotten subdomains.
{% endstep %}
{% endstepper %}

### Public-Facing Assets and Code

{% stepper %}
{% step %}
* #### Search Engine Dorking

Use advanced search operators (Google Dorks, Bing Dorks) to find sensitive files, error messages, login pages, or exposed directories.
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

Search for key employees on platforms like LinkedIn to understand organizational structure, technology stacks, and potential phishing targets.
{% endstep %}

{% step %}
* #### Email Harvesting

Attempt to determine a common email format for the organisation (e.g., `firstname.lastname@target.com`).
{% endstep %}
{% endstepper %}

***

## Active Recon

Active reconnaissance involves direct interaction with the target's network, which increases the likelihood of detection but provides much richer data.

### Host Discovery and Port Scanning

{% stepper %}
{% step %}
* #### Ping Sweeps/ICMP

Identify which hosts are alive on the target network.
{% endstep %}

{% step %}
* #### TCP/UDP Port Scanning (Initial)

Use a tool like Nmap to identify common open ports and the services running on them. Start with less-invasive, non-aggressive scans.
{% endstep %}

{% step %}
* #### Firewall/IDS Evasion

Test methods to bypass basic network defenses. _Potential Link: Tools & Techniques: Nmap Evasion_
{% endstep %}
{% endstepper %}

### Service and Version Enumeration

{% stepper %}
{% step %}
* #### Deep Service Scanning

Re-scan the open ports identified in the initial step to accurately determine the software version, operating system, and specific configurations (e.g., HTTP headers, SSL/TLS certificate details).
{% endstep %}

{% step %}
* #### Web Server Enumeration

Access identified web services to check for common files like `robots.txt`, `sitemap.xml`, or hidden directories. _Potential Link: Web Application Pentest Checklist_
{% endstep %}
{% endstepper %}

### Directory and File Enumeration (For Web Targets)

{% stepper %}
{% step %}
* #### Content Discovery (Fuzzing/Brute-forcing)

Use tools like `ffuf` or `dirbuster` against identified web services to find hidden files, folders, or pages that are not linked from the main site.
{% endstep %}

{% step %}
* #### Virtual Host Enumeration

Check for multiple websites hosted on the same IP address.
{% endstep %}
{% endstepper %}
