# TryHackMe — Hydra Room Writeup

## Overview
This writeup documents my approach to the **Hydra** room on TryHackMe, which focuses on using THC-Hydra to perform brute-force attacks against login services (including HTTP POST forms).

🔗 Room link: https://tryhackme.com/room/hydra

> **Note:** In line with TryHackMe's rules, I have not included the actual flag values in this writeup. This is a methodology-focused writeup meant for learning and portfolio purposes.

## Objective
- Understand how Hydra works as a brute-forcing tool
- Learn to brute-force HTTP POST login forms
- Practice identifying valid vs invalid login responses

## Tools Used
- **Kali Linux** (via VMware, connected over OpenVPN to the TryHackMe network)
- **THC-Hydra** v9.7
- **rockyou.txt** wordlist

## Environment Setup
Connected to the TryHackMe lab network directly from my own Kali VM using OpenVPN instead of the browser AttackBox, to avoid the AttackBox's time limit:

```bash
sudo openvpn <your-ovpn-file>.ovpn
```

Verified connectivity to the target machine:

```bash
ping <TARGET_IP>
```

## Methodology

### 1. Reconnaissance
- Identified the target login page at `/login`
- Inspected the HTML form to find the `username` and `password` field names, and the exact error message shown on failed login (`incorrect`)

### 2. Preparing the wordlist
`rockyou.txt` ships compressed on Kali by default and needed to be extracted:

```bash
sudo gzip -d /usr/share/wordlists/rockyou.txt.gz
```

### 3. Running Hydra
Used Hydra's `http-post-form` module to brute-force the login form:

```bash
hydra -l <username> -P /usr/share/wordlists/rockyou.txt <TARGET_IP> http-post-form \
"/login:username=^USER^&password=^PASS^:F=incorrect"
```

**Command breakdown:**
| Flag | Meaning |
|------|---------|
| `-l` | single known username to test |
| `-P` | path to password wordlist |
| `http-post-form` | tells Hydra the target is an HTTP POST login form |
| `/login:...` | the form path, field names, and failure string Hydra uses to detect a wrong attempt |

### 4. Result
Hydra iterated through the wordlist and identified a valid username/password combination (redacted here per TryHackMe's guidelines).

## Key Takeaways
- How HTTP POST form brute-forcing differs from SSH/FTP brute-forcing in Hydra
- The importance of correctly identifying the failure condition (`F=` string) for accurate results
- Why wordlist choice (e.g. rockyou.txt) matters for real-world password attacks
- Connecting via OpenVPN directly from a personal Kali VM as an alternative to the browser-based AttackBox

## Disclaimer
This writeup is for educational purposes only, based on a legal, intentionally vulnerable TryHackMe lab environment. No real-world systems were targeted.
