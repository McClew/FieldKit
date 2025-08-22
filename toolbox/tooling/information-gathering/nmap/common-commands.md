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

# Common Commands

## Scanning from List in File

{% code overflow="wrap" %}
```bash
sudo  nmap -p 80,443,8000,8080,8180,8888,10000 -iL scope_list 
```
{% endcode %}

