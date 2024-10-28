---
title: Irked
date: 2020-12-06 11:00:00 +/-TTTT
categories: [CTF, hackthebox]
tags: [stego, irc, suid]
---

![infocard](/assets/htb/irked/infocard.png)

Irked is an easy box that has Backdoor Command Execution. we use the Metasploit module to gain a shell on the box. privs is done by using SUID.

## Enumeration

As always we start with Nmap. `nmap -sV -sC -p- $ip > nmap/service.log`

```nmap
Starting Nmap 7.80 ( https://nmap.org ) at 2020-12-06 04:20 EST
Nmap scan report for 10.10.10.117
Host is up (0.33s latency).

PORT     STATE SERVICE VERSION
22/tcp   open  ssh     OpenSSH 6.7p1 Debian 5+deb8u4 (protocol 2.0)
| ssh-hostkey: 
|   256 c8:a3:a2:5e:34:9a:c4:9b:90:53:f7:50:bf:ea:25:3b (ECDSA)
|_  256 8d:1b:43:c7:d0:1a:4c:05:cf:82:ed:c1:01:63:a2:0c (ED25519)
80/tcp   open  http    Apache httpd 2.4.10 ((Debian))
|_http-server-header: Apache/2.4.10 (Debian)
|_http-title: Site doesn't have a title (text/html).
111/tcp  open  rpcbind 2-4 (RPC #100000)
| rpcinfo: 
|   program version    port/proto  service
|   100000  2,3,4        111/tcp   rpcbind
|   100000  2,3,4        111/udp   rpcbind
|   100000  3,4          111/tcp6  rpcbind
|   100000  3,4          111/udp6  rpcbind
|   100024  1          34818/udp   status
|   100024  1          38670/tcp   status
|   100024  1          47035/udp6  status
|_  100024  1          49457/tcp6  status
6697/tcp open  irc     UnrealIRCd
8067/tcp open  irc     UnrealIRCd
Service Info: Host: irked.htb; OS: Linux; CPE: cpe:/o:linux:linux_kernel

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 136.54 seconds
```

In the Nmap scan, we got typical ssh, HTTP service. aside from this we also have a UnrealIRCd installed.
in the Nmap scan we see hostname `irked.htb`. Let's add it in our host file `/etc/hosts` and start further enumeration.

because HTTP has a broad scope of attack let's start with HTTP.

![homepage](/assets/htb/irked/homepage.png)

there is nothing interested on the webpage. we only got a smiley emoji. it may be has some steganography. so let's download the image and start directory brute-forcing.

![ffuf](/assets/htb/irked/ffuf.png)

we only have `manual` which is normal apache documentation.

we do not have much on the webpage. so I decide to test for IRC.

first, we connect the IRC using some random nickname and then try to enumerate some info.

![ircreg](/assets/htb/irked/ircreg.png)

 use `INFO` cmd to get info about the irc server.

![info](/assets/htb/irked/version.png)

we got UnrealIRCd version.

using searchsploit we found Backdoor Command Execution for the same version. exploit have Metasploit module so let's fire up Metasploit and gain initial access.

## Gaining Shell

using `/exploit/unix/irc/unreal_ircd_3281_backdoor` module got shell on box.

![shell](/assets/htb/irked/newshell.png)

we got a shell on the box as an ircd user.

i can't find  `user.txt` in ircd home directory. use find cmd to find out `user.txt`
`find / -type f -name user.txt 2>/dev/null`

![userflagpath](/assets/htb/irked/userflagpath.png)

I tried to read the user flag but we don't have permission.

so have to escalate our privileges to `djmardov`

after bit of enumeration i found `.backup` file in
`/home/djmardov/Documents` directory.
when we cat `.backup` file we got a password and a hint.

![backup](/assets/htb/irked/backup.png)

it says this password for steg. we have only one image `irked.jpg` from the webpage.
Let's use `steghide`  and try to extract data from `irked.jpg` .

`steghide extract -sf irked.jpg`

![steghide](/assets/htb/irked/steghide.png)

we got another password. It's not a base64 string because of `:`.

Let's try this password for `djmardov` account.

![user](/assets/htb/irked/user.png)

we got a user flag.

## How exploit works

this specific version 3.2.8.1 is contain backdoor . this backdoor is used by attacker to execute any cmd on system.

vuln backdoor code :
```
 #ifdef DEBUGMODE3
	   if (!memcmp(readbuf, DEBUGMODE3_INFO, 2))
	       DEBUG3_LOG(readbuf);
 #endif
```
it looks like some debug statement. `DEBUG3_LOG` is call to `system()` and `DEBUGMOD3_INFO` is just string `AB` . so if we send any cmd after `AB` it directly send to `system()` .

## Getting root

after bit enumeration we found suspicious SUID `/usr/bin/viewuser`

![suid](/assets/htb/irked/suid.png)

when we run binary it tries to execute `listusers` in `tmp` directory. but the file is not present.

we can create a file named `listusers` and add a simple bash script to get shell.

![listuser](/assets/htb/irked/listuser.png)

give execution permission to file. `chmod +x listusers`

by running `/usr/bin/viewuser` we get the root shell.

![root](/assets/htb/irked/root.png)

