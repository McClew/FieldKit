# rpcclient

## **Starting a Session**

```bash
rpcclient -U <username>[%<password>] <target_server_IP_or_hostname>
```

#### Example:

```bash
rpcclient -U Administrator%MySecretPassw0rd! 192.168.1.100
```

## **Obtaining Password Policy**

```bash
querydominfo
```

#### Expected Output:

```bash
rpcclient $> querydominfo

Domain:		INLANEFREIGHT
Server:		
Comment:	
Total Users:	3650
Total Groups:	0
Total Aliases:	37
Sequence No:	1
Force Logoff:	-1
Domain Server State:	0x1
Server Role:	ROLE_DOMAIN_PDC
Unknown 3:	0x1
rpcclient $> getdompwinfo
min_password_length: 8
password_properties: 0x00000001
	DOMAIN_PASSWORD_COMPLEX
```
