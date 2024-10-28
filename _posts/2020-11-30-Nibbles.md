---
title: Nibbles
date: 2020-11-30 22:30 +/-TTTT
categories: [CTF, hackthebox]
tags: [file upload, permission]
---

![infocard](/assets/htb/nibble/infocard.png)

Nibble is an easy box that uses the default credential for the admin account. also, this version is (4.0.3) is vulnerable to an arbitrary file upload to gain user shell. for privs escalation we use misconfigured permission script. 

## Enumeration

as always we start with a Nmap scan.
```
Starting Nmap 7.80 ( https://nmap.org ) at 2020-12-01 11:26 EST
Nmap scan report for 10.10.10.75
Host is up (0.19s latency).

PORT   STATE SERVICE VERSION
22/tcp open  ssh     OpenSSH 7.2p2 Ubuntu 4ubuntu2.2 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   2048 c4:f8:ad:e8:f8:04:77:de:cf:15:0d:63:0a:18:7e:49 (RSA)
|   256 22:8f:b1:97:bf:0f:17:08:fc:7e:2c:8f:e9:77:3a:48 (ECDSA)
|_  256 e6:ac:27:a3:b5:a9:f1:12:3c:34:a5:5d:5b:eb:3d:e9 (ED25519)
80/tcp open  http    Apache httpd 2.4.18 ((Ubuntu))
|_http-server-header: Apache/2.4.18 (Ubuntu)
|_http-title: Site doesn't have a title (text/html).
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 14.65 seconds
```

we found two open ports. ssh and webpage hosted on the machine.

Let's enumerate the webpage first. by viewing source code we found a directory named nibble.

![nibbledir](/assets/htb/nibble/nibble.png)

after fuzzing for the directory we found some entries. it has an admin login page at `admin.php`.

![ffuf](/assets/htb/nibble/ffuf.png)

## Gaining Shell

in `nibbleblog/content/private/users.xml` we found username `admin`

![admin](/assets/htb/nibble/admin.png)

tried creds `admin` and `nibbles` get into the admin dashboard.

Let's quick search for nibble blog exploits. It shows two exploits.

![searchsploit](/assets/htb/nibble/searchsploit.png)

By arbitrary file upload exploit with the above creds we got a user shell.

![user](/assets/htb/nibble/user.png)

## Getting Root

in-home directory there is file `personal.zip` unzip that file. it has the script `monitor.sh` which has 777 permission. that means we can use that script to get root access.

![root](/assets/htb/nibble/root.png)

there is also an outdated kernel version which is vulnerable to a kernel exploit but I didn't try it.