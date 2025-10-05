# DNS Reverse Lookup Brute Force

If a domain has PTR Record configured, domain names might be found using a reverse lookup script:

```sh
for ip in $(seq 155 190); do host 50.7.67.$ip; done | grep -v "not found"
```
