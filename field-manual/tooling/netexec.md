# NetExec

{% hint style="info" %}
## Download & Install:

[https://github.com/Pennyw0rth/NetExec](https://github.com/Pennyw0rth/NetExec)
{% endhint %}

The general format for using NetExec is as follows:

```bash
clue@machine[/]$ netexec <proto> <target-IP> -u <user or userlist> -p <password or passwordlist>
```

## Map Network Hosts <a href="#map-network-hosts" id="map-network-hosts"></a>

Returns a list of live hosts

```bash
nxc smb 192.168.1.0/24
```

Expected Results:

```bash
SMB         192.168.1.101    445    DC2012A          [*] Windows Server 2012 R2 Standard 9600 x64 (name:DC2012A) (domain:OCEAN) (signing:True) (SMBv1:True)
SMB         192.168.1.102    445    DC2012B          [*] Windows Server 2012 R2 Standard 9600 x64 (name:DC2012B) (domain:EARTH) (signing:True) (SMBv1:True)
SMB         192.168.1.110    445    DC2016A          [*] Windows Server 2016 Standard Evaluation 14393 x64 (name:DC2016A) (domain:OCEAN) (signing:True) (SMBv1:True)
SMB         192.168.1.117    445    WIN10DESK1       [*] WIN10DESK1 x64 (name:WIN10DESK1) (domain:OCEAN) (signing:False) (SMBv1:True)
```

## Enumerate Null Sessions

Check if **Null Session**, also known as Anonymous session, is enabled on the network. Can be very useful on a Domain Controller to enumerate users, groups, password policies, etc.

```bash
nxc smb 10.10.10.161 -u '' -p ''
nxc smb 10.10.10.161 -u '' -p '' --shares
nxc smb 10.10.10.161 -u '' -p '' --pass-pol
nxc smb 10.10.10.161 -u '' -p '' --users
nxc smb 10.10.10.161 -u '' -p '' --groups
```

You can also reproduce this behavior with `smbclient` or `rpcclient`

```bash
smbclient -N -U "" -L \\10.10.10.161
```

```bash
rpcclient -N -U "" -L \\10.10.10.161
rpcclient $> enumdomusers
user:[bonclay] rid:[0x46e]
user:[zoro] rid:[0x46f]
```
