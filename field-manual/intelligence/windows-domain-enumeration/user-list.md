---
icon: user
---

# User List

## SMB NULL Session

### Tools

<table><thead><tr><th width="196">Name</th><th>Description</th></tr></thead><tbody><tr><td><a data-mention href="../../../toolbox/tooling/crackmapexec.md">crackmapexec.md</a></td><td>An open-source tool that automates the assessment of Active Directory networks by leveraging various protocols to perform reconnaissance, credential gathering, lateral movement, and exploitation.</td></tr><tr><td><a data-mention href="../../../toolbox/tooling/rpcclient.md">rpcclient.md</a></td><td>A command-line tool, part of the Samba suite, used to connect to and interact with Windows SMB/MS-RPC services, primarily for administration and network enumeration of users, groups, shares, and other domain information.</td></tr><tr><td><a data-mention href="../../../toolbox/tooling/enum4linux.md">enum4linux.md</a></td><td>A Perl script that wraps around various Samba tools (like <code>smbclient</code> and <code>rpcclient</code>) to automate information gathering from Windows and Samba systems, including users, groups, shares, and password policies.</td></tr></tbody></table>

### enum4linux

We can use the `enumdomusers` command after connecting anonymously using `rpcclient`.

```bash
clue@machine[~]$ enum4linux -U $IP  | grep "user:" | cut -f2 -d"[" | cut -f1 -d"]"

administrator
guest
krbtgt
lab_adm
student
avazquez
pfalcon
fanthony
wdillard
lbradford
sgage
asanchez
dbranch
ccruz
njohnson
mholliday
<SNIP>
```

### rpcclient

Use `CrackMapExec` with the `--users` flag. This is a useful tool that will also show the `badpwdcount` (invalid login attempts), so we can remove any accounts from our list that are close to the lockout threshold.

It also shows the `baddpwdtime`, which is the date and time of the last bad password attempt, so we can see how close an account is to having its `badpwdcount` reset.

```bash
clue@machine[~]$ rpcclient -U "" -N $IP

rpcclient $> enumdomusers 
user:[administrator] rid:[0x1f4]
user:[guest] rid:[0x1f5]
user:[krbtgt] rid:[0x1f6]
user:[lab_adm] rid:[0x3e9]
user:[htb-student] rid:[0x457]
user:[avazquez] rid:[0x458]

<SNIP>
```

{% hint style="info" %}
In an environment with multiple Domain Controllers, this value is maintained separately on each one. To get an accurate total of the account's bad password attempts, we would have to either query each Domain Controller and use the sum of the values or query the Domain Controller with the PDC Emulator FSMO role.
{% endhint %}

### CrackMapExec

```bash
clue@machine[~]$ crackmapexec smb $IP --users

SMB         172.16.5.5      445    ACADEMY-EA-DC01  [*] Windows 10.0 Build 17763 x64 (name:ACADEMY-EA-DC01) (domain:INLANEFREIGHT.LOCAL) (signing:True) (SMBv1:False)
SMB         172.16.5.5      445    ACADEMY-EA-DC01  [+] Enumerated domain user(s)
SMB         172.16.5.5      445    ACADEMY-EA-DC01  INLANEFREIGHT.LOCAL\administrator                  badpwdcount: 0 baddpwdtime: 2022-01-10 13:23:09.463228
SMB         172.16.5.5      445    ACADEMY-EA-DC01  INLANEFREIGHT.LOCAL\guest                          badpwdcount: 0 baddpwdtime: 1600-12-31 19:03:58
SMB         172.16.5.5      445    ACADEMY-EA-DC01  INLANEFREIGHT.LOCAL\lab_adm                        badpwdcount: 0 baddpwdtime: 2021-12-21 14:10:56.859064
SMB         172.16.5.5      445    ACADEMY-EA-DC01  INLANEFREIGHT.LOCAL\krbtgt                         badpwdcount: 0 baddpwdtime: 1600-12-31 19:03:58
SMB         172.16.5.5      445    ACADEMY-EA-DC01  INLANEFREIGHT.LOCAL\htb-student                    badpwdcount: 0 baddpwdtime: 2022-02-22 14:48:26.653366
SMB         172.16.5.5      445    ACADEMY-EA-DC01  INLANEFREIGHT.LOCAL\avazquez                       badpwdcount: 0 baddpwdtime: 2022-02-17 22:59:22.684613

<SNIP>
```

## LDAP Anonymous Bind

### Tools

<table><thead><tr><th width="213">Name</th><th>Description</th></tr></thead><tbody><tr><td>ldapsearch</td><td>A command-line utility for querying LDAP directories, including Active Directory, to retrieve specific entries and attributes based on defined filters and scope.</td></tr><tr><td>windapsearch.py</td><td>A Python script for enumerating users, groups, computers, and other objects from Active Directory domains by querying the LDAP protocol.</td></tr></tbody></table>

### ldapsearch

```bash
clue@machine[~]$ ldapsearch -h $IP -x -b "DC=ACME,DC=LOCAL" -s sub "(&(objectclass=user))"  | grep sAMAccountName: | cut -f2 -d" "

guest
ACME-EA-DC01$
ACME-EA-MS01$
ACME-EA-WEB01$
student
avazquez
pfalcon
fanthony
wdillard
lbradford
sgage
asanchez
dbranch

<SNIP>
```

{% hint style="info" %}
When using `ldapsearch` you need to specify a valid LDAP search filter.
{% endhint %}

### windapsearch

```bash
clue@machine[~]$ ./windapsearch.py --dc-ip $IP -u "" -U

[+] No username provided. Will try anonymous bind.
[+] Using Domain Controller at: 172.16.5.5
[+] Getting defaultNamingContext from Root DSE
[+]	Found: DC=INLANEFREIGHT,DC=LOCAL
[+] Attempting bind
[+]	...success! Binded as: 
[+]	 None

[+] Enumerating all AD users
[+]	Found 2906 users: 

cn: Guest

cn: Htb Student
userPrincipalName: htb-student@inlanefreight.local

cn: Annie Vazquez
userPrincipalName: avazquez@inlanefreight.local

cn: Paul Falcon
userPrincipalName: pfalcon@inlanefreight.local

cn: Fae Anthony
userPrincipalName: fanthony@inlanefreight.local

cn: Walter Dillard
userPrincipalName: wdillard@inlanefreight.local

<SNIP>
```

{% hint style="info" %}
Specify anonymous access by providing a blank username with the `-u` flag and the `-U` flag to tell the tool to retrieve just users.
{% endhint %}

## Kerberose Pre-Authentication

### Kerbrute

If you have no access at all to our internal network, we can use `Kerbrute` to enumerate valid AD accounts and for password spraying.

The tool sends TGT requests to the domain controller without Kerberos Pre-Authentication to perform username enumeration. If the KDC responds with the error `PRINCIPAL UNKNOWN`, the username is invalid. Whenever the KDC prompts for Kerberos Pre-Authentication, this signals that the username exists, and the tool will mark it as valid.

{% hint style="info" %}
This method of username enumeration does not generate Windows event ID [4625: An account failed to log on](https://docs.microsoft.com/en-us/windows/security/threat-protection/auditing/event-4625), or a logon failure which is often monitored for, and will not lock out accounts.
{% endhint %}

```bash
clue@machine[~]$  kerbrute userenum -d acme.local --dc $IP /opt/jsmith.txt 

    __             __               __     
   / /_____  _____/ /_  _______  __/ /____ 
  / //_/ _ \/ ___/ __ \/ ___/ / / / __/ _ \
 / ,< /  __/ /  / /_/ / /  / /_/ / /_/  __/
/_/|_|\___/_/  /_.___/_/   \__,_/\__/\___/                                        

Version: dev (9cfb81e) - 02/17/22 - Ronnie Flathers @ropnop

2022/02/17 22:16:11 >  Using KDC(s):
2022/02/17 22:16:11 >  	172.16.5.5:88

2022/02/17 22:16:11 >  [+] VALID USERNAME:	 jjones@acme.local
2022/02/17 22:16:11 >  [+] VALID USERNAME:	 sbrown@acme.local
2022/02/17 22:16:11 >  [+] VALID USERNAME:	 tjohnson@acme.local
2022/02/17 22:16:11 >  [+] VALID USERNAME:	 jwilson@acme.local
2022/02/17 22:16:11 >  [+] VALID USERNAME:	 bdavis@acme.local
2022/02/17 22:16:11 >  [+] VALID USERNAME:	 njohnson@acme.local
2022/02/17 22:16:11 >  [+] VALID USERNAME:	 asanchez@acme.local
2022/02/17 22:16:11 >  [+] VALID USERNAME:	 dlewis@acme.local
2022/02/17 22:16:11 >  [+] VALID USERNAME:	 ccruz@acme.local

<SNIP>
```

{% hint style="warning" %}
Using Kerbrute for username enumeration will generate event ID [4768: A Kerberos authentication ticket (TGT) was requested](https://docs.microsoft.com/en-us/windows/security/threat-protection/auditing/event-4768). This will only be triggered if [Kerberos event logging](https://docs.microsoft.com/en-us/troubleshoot/windows-server/identity/enable-kerberos-event-logging) is enabled via Group Policy.
{% endhint %}

{% hint style="success" %}
&#x20;Defenders can tune their SIEM tools to look for an influx of this event ID, which may indicate an attack. If we are successful with this method during a penetration test, this can be an excellent recommendation to add to our report.
{% endhint %}

## Credentialed Enumeration

### Tools

<table><thead><tr><th width="196">Name</th><th>Description</th></tr></thead><tbody><tr><td><a data-mention href="../../../toolbox/tooling/crackmapexec.md">crackmapexec.md</a></td><td>An open-source tool that automates the assessment of Active Directory networks by leveraging various protocols to perform reconnaissance, credential gathering, lateral movement, and exploitation.</td></tr></tbody></table>

### CrackMapExec

```bash
clue@machine[~]$ sudo crackmapexec smb $IP -u acmeadmin -p Acme_admin_AD! --users

[sudo] password for acmeadmin: 
SMB         172.16.5.5      445    ACME-EA-DC01  [*] Windows 10.0 Build 17763 x64 (name:ACME-EA-DC01) (domain:ACME.LOCAL) (signing:True) (SMBv1:False)
SMB         172.16.5.5      445    ACME-EA-DC01  [+] INLANEFREIGHT.LOCAL\acmeadmin:Acme_admin_AD! 
SMB         172.16.5.5      445    ACME-EA-DC01  [+] Enumerated domain user(s)
SMB         172.16.5.5      445    ACME-EA-DC01  ACME.LOCAL\administrator                  badpwdcount: 1 baddpwdtime: 2022-02-23 21:43:35.059620
SMB         172.16.5.5      445    ACME-EA-DC01  ACME.LOCAL\guest                          badpwdcount: 0 baddpwdtime: 1600-12-31 19:03:58
SMB         172.16.5.5      445    ACME-EA-DC01  ACME.LOCAL\lab_adm                        badpwdcount: 0 baddpwdtime: 2021-12-21 14:10:56.859064
SMB         172.16.5.5      445    ACME-EA-DC01  ACME.LOCAL\krbtgt                         badpwdcount: 0 baddpwdtime: 1600-12-31 19:03:58
SMB         172.16.5.5      445    ACME-EA-DC01  ACME.LOCAL\acmeadmin                      badpwdcount: 0 baddpwdtime: 2022-02-22 14:48:26.653366
SMB         172.16.5.5      445    ACME-EA-DC01  ACME.LOCAL\avazquez                       badpwdcount: 20 baddpwdtime: 2022-02-17 22:59:22.684613
SMB         172.16.5.5      445    ACME-EA-DC01  ACME.LOCAL\pfalcon                        badpwdcount: 0 baddpwdtime: 1600-12-31 19:03:58
<SNIP>
```
