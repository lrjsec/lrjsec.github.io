---
title: "TryHackMe: Silver Platter"
author: lrjsec
categories: [TryHackMe]
tags: [web, brute-force, caido, idor, ssh, ]
render_with_liquid: false
media_subpath: /images/thm_silver_platter/
image:
  path: room_image.webp
---

**Silver Platter** is a cool room for exploring web app pentesting using techniques like custom wordlist using cewl to gain access to **Silverpeas**. By then exploiting a vulnerability that allows an authenticated user to read others messages we end up finding **SSH** credentials in one of them.

[![Tryhackme Room Link](room_card.webp){: width="300" height="300" .shadow}](https://tryhackme.com/r/room/silverplatter){: .center }

## Initial Enumeration

### Nmap/Rust Scan

We start with a `rustscan` / `nmap` scan.

```console
PORT     STATE SERVICE    REASON         VERSION
22/tcp   open  ssh        syn-ack ttl 61 OpenSSH 8.9p1 Ubuntu 3ubuntu0.4 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   256 1b:1c:87:8a:fe:34:16:c9:f7:82:37:2b:10:8f:8b:f1 (ECDSA)
| ecdsa-sha2-nistp256 AAAAE2VjZHNhLXNoYTItbmlzdHAyNTYAAAAIbmlzdHAyNTYAAABBBJ0ia1tcuNvK0lfuy3Ep2dsElFfxouO3VghX5Rltu77M33pFvTeCn9t5A8NReq3felAqPi+p+/0eRRfYuaeHRT4=
|   256 26:6d:17:ed:83:9e:4f:2d:f6:cd:53:17:c8:80:3d:09 (ED25519)
|_ssh-ed25519 AAAAC3NzaC1lZDI1NTE5AAAAIKecigNtiy6tW5ojXM3xQkbtTOwK+vqvMoJZnIxVowju

80/tcp   open  http       syn-ack ttl 61 nginx 1.18.0 (Ubuntu)
|_http-title: Hack Smarter Security
|_http-server-header: nginx/1.18.0 (Ubuntu)
| http-methods: 
|_  Supported Methods: GET HEAD

8080/tcp open  http-proxy syn-ack ttl 60
| fingerprint-strings: 
|   FourOhFourRequest: 
|     HTTP/1.1 404 Not Found
|     Connection: close
|     Content-Length: 74
|     Content-Type: text/html
|     Date: Thu, 16 Jan 2025 00:23:34 GMT
|     <html><head><title>Error</title></head><body>404 - Not Found</body></html>
|   GenericLines, Help, Kerberos, LDAPSearchReq, LPDString, RTSPRequest, SMBProgNeg, SSLSessionReq, Socks5, TLSSessionReq, TerminalServerCookie: 
|     HTTP/1.1 400 Bad Request
|     Content-Length: 0
|     Connection: close
|   GetRequest, HTTPOptions: 
|     HTTP/1.1 404 Not Found
|     Connection: close
|     Content-Length: 74
|     Content-Type: text/html
|     Date: Thu, 16 Jan 2025 00:23:33 GMT
|_    <html><head><title>Error</title></head><body>404 - Not Found</body></html>
```

There are three open ports:

- **22** (`SSH`)
- **80** (`HTTP`)
- **8080** (`HTTP`)

### SSH 22

I first check to see if password authentication is even enabled

```
┌──(kali㉿kali)-[~/THM/ctfs/SilverPlatter]
└─$ ssh root@silver.local 
root@silver.local's password:
```

Great!, I put that to the side and continue on. . .

### Web 80

Checking `http://10.10.191.243/`, we find a static site.

![Web 80 Index](web_80_index.png){: width="1200" height="600"}

I interact with the entire page and a find something interesting

(IMG)
