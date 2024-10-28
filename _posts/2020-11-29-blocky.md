---
title: Blocky
date: 2020-11-29 22:43:43 +/-TTTT
categories: [CTF, hackthebox]
tags: [jar, sudo]
---

![infocard](/assets/htb/blocky/infocard.png)

Blocky is an easy box that involves a jar file to get user shell and sudo for privs escalation.

## Enumeration

As always we start enumeration with Nmap.

```
Starting Nmap 7.80 ( https://nmap.org ) at 2020-11-29 07:25 EST
Nmap scan report for 10.10.10.37
Host is up (0.43s latency).

PORT      STATE  SERVICE   VERSION
21/tcp    open   ftp       ProFTPD 1.3.5a
22/tcp    open   ssh       OpenSSH 7.2p2 Ubuntu 4ubuntu2.2 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   2048 d6:2b:99:b4:d5:e7:53:ce:2b:fc:b5:d7:9d:79:fb:a2 (RSA)
|   256 5d:7f:38:95:70:c9:be:ac:67:a0:1e:86:e7:97:84:03 (ECDSA)
|_  256 09:d5:c2:04:95:1a:90:ef:87:56:25:97:df:83:70:67 (ED25519)
80/tcp    open   http      Apache httpd 2.4.18 ((Ubuntu))
|_http-generator: WordPress 4.8
|_http-server-header: Apache/2.4.18 (Ubuntu)
|_http-title: BlockyCraft &#8211; Under Construction!
8192/tcp  closed sophos
25565/tcp open   minecraft Minecraft 1.11.2 (Protocol: 127, Message: A Minecraft Server, Users: 0/20)
Service Info: OSs: Unix, Linux; CPE: cpe:/o:linux:linux_kernel

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 27.42 seconds
```
We have FTP, a webpage, and a Minecraft server to test. I always start with FTP. Search a little bit about the version but doesn't find any exploit or vulnerability for this version. so let's move to webpage.

WordPress is used for a webpage. put `wpscan` in the background and enumerate other things.

in `wpscan` we find username `notch`. add it in our notes and enumerate furthermore.

Let's find out what directories are available on the webpage.

```
Gobuster v3.0.1
by OJ Reeves (@TheColonial) & Christian Mehlmauer (@_FireFart_)
===============================================================
[+] Url:            http://10.10.10.37
[+] Threads:        10
[+] Wordlist:       /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt
[+] Status codes:   200,204,301,302,307,401,403
[+] User Agent:     gobuster/3.0.1
[+] Extensions:     php
[+] Timeout:        10s
===============================================================
2020/11/29 08:24:15 Starting gobuster
===============================================================
/index.php (Status: 301)
/wiki (Status: 301)
/wp-content (Status: 301)
/wp-login.php (Status: 200)
/plugins (Status: 301)
/wp-includes (Status: 301)
/javascript (Status: 301)
/wp-trackback.php (Status: 200)
/wp-admin (Status: 301)
```
in `plugins` directory there are two jar file.

![plugins](/assets/htb/blocky/plugins.png)

Download both files and unzip them. in `BlockyCore.jar` found `BlockyCore.class` lets decompile it. i doesn't have jad on my machines so I use online decompiler and decompile BlockyCor.jar file.

After decompiling file, we got the sql database password for the root user.

![decomiple](/assets/htb/blocky/decompile.png)

tried to ssh on the box using password and user `notch`. login into ssh.

![user](/assets/htb/blocky/user.png)

## Getting root

by typing `sudo -l` it shows that we can run All cmd as `root`. so to get root shell just run `sudo su` and got root access.

![root](/assets/htb/blocky/root.png)