---
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

# Windows User & Group Enumeration

## Current User

When we gain access to a host, we should always check what user context our account is running under first. Sometimes, we are already SYSTEM or equivalent!&#x20;

```sh
C:\> echo %USERNAME%

htb-student
```

### Current User Privileges

Knowing what privileges our user has can greatly help in escalating privileges. Suppose we gain access as a service account. In that case, we may have privileges such as `SeImpersonatePrivilege`, which can often be easily abused to escalate privileges using a tool such as [Juicy Potato](https://github.com/ohpe/juicy-potato).

```sh
C:\> whoami /priv

PRIVILEGES INFORMATION
----------------------

Privilege Name                Description                    State
============================= ============================== ========
SeChangeNotifyPrivilege       Bypass traverse checking       Enabled
SeIncreaseWorkingSetPrivilege Increase a process working set Disabled
```

{% hint style="warning" %}
#### Token Dependant Output

The `whoami /priv` output is limited to the privileges held by the current process token. This list is generally shorter than the full set of privileges associated with the underlying user account, as some privileges will not show unless the command is ran with a higher privileged (elevated) token.
{% endhint %}

### Current User Groups

Has our user inherited any rights through their group membership? Are they privileged in the Active Directory domain environment, which could be leveraged to gain access to more systems?

```sh
C:\> whoami /groups

GROUP INFORMATION
-----------------

Group Name                             Type             SID          Attributes
====================================== ================ ============ ==================================================
Everyone                               Well-known group S-1-1-0      Mandatory group, Enabled by default, Enabled group
BUILTIN\Remote Desktop Users           Alias            S-1-5-32-555 Mandatory group, Enabled by default, Enabled group
BUILTIN\Users                          Alias            S-1-5-32-545 Mandatory group, Enabled by default, Enabled group
NT AUTHORITY\REMOTE INTERACTIVE LOGON  Well-known group S-1-5-14     Mandatory group, Enabled by default, Enabled group
NT AUTHORITY\INTERACTIVE               Well-known group S-1-5-4      Mandatory group, Enabled by default, Enabled group
NT AUTHORITY\Authenticated Users       Well-known group S-1-5-11     Mandatory group, Enabled by default, Enabled group
NT AUTHORITY\This Organization         Well-known group S-1-5-15     Mandatory group, Enabled by default, Enabled group
NT AUTHORITY\Local account             Well-known group S-1-5-113    Mandatory group, Enabled by default, Enabled group
LOCAL                                  Well-known group S-1-2-0      Mandatory group, Enabled by default, Enabled group
NT AUTHORITY\NTLM Authentication       Well-known group S-1-5-64-10  Mandatory group, Enabled by default, Enabled group
Mandatory Label\Medium Mandatory Level Label            S-1-16-8192
```

## Users

### Logged in Users

It is always important to determine what users are logged into a system. Are they idle or active? Can we determine what they are working on? While more challenging to pull off, we can sometimes attack users directly to escalate privileges or gain further access. During an evasive engagement, we would need to tread lightly on a host with other user(s) actively working on it to avoid detection.

```sh
C:\> query user

 USERNAME              SESSIONNAME        ID  STATE   IDLE TIME  LOGON TIME
>administrator         rdp-tcp#2           1  Active          .  3/25/2021 9:27 AM
```

### All Users

Knowing what other users are on the system is important as well. If we gained RDP access to a host using credentials we captured for a user `bob`, and see a `bob_adm` user in the local administrators group, it is worth checking for credential re-use. Can we access the user profile directory for any important users? We may find valuable files such as scripts with passwords or SSH keys in a user's Desktop, Documents, or Downloads folder.

```sh
C:\> net user

User accounts for \\WINLPE-SRV01

-------------------------------------------------------------------------------
Administrator            DefaultAccount           Guest
helpdesk                 htb-student              jordan
sarah                    secsvc
The command completed successfully.
```

## Groups

### All Groups

Knowing what non-standard groups are present on the host can help us determine what the host is used for, how heavily accessed it is, or may even lead to discovering a misconfiguration such as all Domain Users in the Remote Desktop or local administrators groups.

```sh
C:\> net localgroup

Aliases for \\WINLPE-SRV01

-------------------------------------------------------------------------------
*Access Control Assistance Operators
*Administrators
*Backup Operators
*Certificate Service DCOM Access
*Cryptographic Operators
*Distributed COM Users
*Event Log Readers
*Guests
*Hyper-V Administrators
*IIS_IUSRS
*Network Configuration Operators
*Performance Log Users
*Performance Monitor Users
*Power Users
*Print Operators
*RDS Endpoint Servers
*RDS Management Servers
*RDS Remote Access Servers
*Remote Desktop Users
*Remote Management Users
*Replicator
*Storage Replica Administrators
*System Managed Accounts Group
*Users
The command completed successfully.
```

### **Details About a Group**

It is worth checking out the details for any non-standard groups. Though unlikely, we may find a password or other interesting information stored in the group's description. During our enumeration, we may discover credentials of another non-admin user who is a member of a local group that can be leveraged to escalate privileges.

## Password Enumeration

### Password Policy

```sh
C:\> net accounts

Force user logoff how long after time expires?:       Never
Minimum password age (days):                          0
Maximum password age (days):                          42
Minimum password length:                              0
Length of password history maintained:                None
Lockout threshold:                                    Never
Lockout duration (minutes):                           30
Lockout observation window (minutes):                 30
Computer role:                                        SERVER
The command completed successfully.
```
