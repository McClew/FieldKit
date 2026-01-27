---
icon: microsoft
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

# Windows Enumeration

## Internal Enumeration

\-

### System & Patch Analysis

The initial phase of local enumeration involves identifying the machines configuration and security posture to find escalation paths.

{% stepper %}
{% step %}
#### System Information

Use the systeminfo command to gather basic OS details, architecture and a list of installed hotfixes.
{% endstep %}

{% step %}
#### Update Verification

To specifically target missing patches we can use:

```bash
wmic qfe list brief
```

```powershell
Get-Hotfix
```
{% endstep %}

{% step %}
#### Exploit Suggestions

Tools like `Watson` and `WES-NG` take this system information as input to identify specific missing KBs and suggest relevant privilege escalation paths.
{% endstep %}
{% endstepper %}

### Software & Process Discovery

Identifying non-standard software may run with elevated privileges or contain unique vulnerabilities.

{% stepper %}
{% step %}
#### Application Inventory

Use `wmic product get name` to list installed software.
{% endstep %}

{% step %}
#### Service & Process Mapping

Map PIDs to listening ports and running services using `netstat -ano`, `get-process` and `get-service`.
{% endstep %}
{% endstepper %}

### Privilege & Group Enumeration

Understanding the context of the current user account is essential for determining next steps in an attack chain.

{% stepper %}
{% step %}
#### Local Group Membership

Review which local groups the user belongs to, such as Event Log readers or Account Operators as these grant specific rights that can be abused.
{% endstep %}

{% step %}
#### Named Pipe Permissions

Use accesschk to find named pipes with lax permissions (e.g., FILE\_ALL\_ACCESS for the "Everyone" group), which can often be leveraged for SYSTEM-level escalation.
{% endstep %}
{% endstepper %}

### Credential Harvesting & Log Review

Windows hosts often store sensitive data in the registry or logs that can be extracted with local administrative access.

{% stepper %}
{% step %}
#### Registry Hive Dumping

Use `reg.exe save` to copy the SAM, SYSTEM and SECURITY hives.
{% endstep %}

{% step %}
#### Log Hunting

If process creation auditng is enabled, search Event ID 4688 (New Process Created) for cleartext passwords passed as command-line arguments using wevutil or Get-WinEvent.
{% endstep %}
{% endstepper %}

## Automated Enumeration

\-
