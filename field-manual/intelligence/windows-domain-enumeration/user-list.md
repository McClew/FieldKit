---
icon: user
---

# User List

## SMB NULL Session

### Tools

<table><thead><tr><th width="196">Name</th><th>Description</th></tr></thead><tbody><tr><td><a data-mention href="../../tooling/crackmapexec.md">crackmapexec.md</a></td><td>An open-source tool that automates the assessment of Active Directory networks by leveraging various protocols to perform reconnaissance, credential gathering, lateral movement, and exploitation.</td></tr><tr><td><a data-mention href="../../tooling/rpcclient.md">rpcclient.md</a></td><td>A command-line tool, part of the Samba suite, used to connect to and interact with Windows SMB/MS-RPC services, primarily for administration and network enumeration of users, groups, shares, and other domain information.</td></tr><tr><td><a data-mention href="../../tooling/enum4linux.md">enum4linux.md</a></td><td>A Perl script that wraps around various Samba tools (like <code>smbclient</code> and <code>rpcclient</code>) to automate information gathering from Windows and Samba systems, including users, groups, shares, and password policies.</td></tr></tbody></table>

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
