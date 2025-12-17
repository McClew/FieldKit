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

# windapsearch

`windapsearch` is a Python script to help enumerate users, groups and computers from a Windows domain through LDAP queries. By default, Windows Domain Controllers support basic LDAP operations through port 389/tcp. With any valid domain account (regardless of privileges), it is possible to perform LDAP queries against a domain controller for any AD related information.

{% hint style="info" %}
#### Download & Install

[https://github.com/ropnop/windapsearch](https://github.com/ropnop/windapsearch)
{% endhint %}

{% hint style="warning" %}
#### Depreciated

This tool has been superseded by go-windapsearch.

[https://github.com/ropnop/go-windapsearch](https://github.com/ropnop/go-windapsearch)
{% endhint %}
