# Nikto

| Download                       |
| ------------------------------ |
| https://github.com/sullo/nikto |

**Nikto** is a powerful open-source web server scanner. In addition to its primary function as a vulnerability assessment tool, Nikto's fingerprinting capabilities provide insights into a website's technology stack.

### Install

```bash
sudo apt update && sudo apt install -y perl
```

```bash
git clone https://github.com/sullo/nikto
```

```bash
cd nikto/program
```

```bash
chmod +x ./nikto.pl
```

### Fingerprinting Scan

```bash
nikto -h acme.com -Tuning b
```
