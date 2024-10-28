---
title: Lame
date: 2020-12-04 13:49:49 +/-TTTT
categories: [CTF, hackthebox]
tags: [ftp, smb]
---

![infocard](/assets/htb/lame/infocard.png)

lame is an easy box and one of the earliest boxes. and with Metasploit, we can root the box.

## Enumeration

as always we start the box with a Nmap scan.

```nmap
Starting Nmap 7.80 ( https://nmap.org ) at 2020-11-30 02:48 EST
Nmap scan report for 10.10.10.3
Host is up (0.36s latency).

PORT     STATE SERVICE     VERSION
21/tcp   open  ftp         vsftpd 2.3.4
|_ftp-anon: Anonymous FTP login allowed (FTP code 230)
| ftp-syst: 
|   STAT: 
| FTP server status:
|      Connected to 10.10.14.7
|      Logged in as ftp
|      TYPE: ASCII
|      No session bandwidth limit
|      Session timeout in seconds is 300
|      Control connection is plain text
|      Data connections will be plain text
|      vsFTPd 2.3.4 - secure, fast, stable
|_End of status
22/tcp   open  ssh         OpenSSH 4.7p1 Debian 8ubuntu1 (protocol 2.0)
| ssh-hostkey: 
|   1024 60:0f:cf:e1:c0:5f:6a:74:d6:90:24:fa:c4:d5:6c:cd (DSA)
|_  2048 56:56:24:0f:21:1d:de:a7:2b:ae:61:b1:24:3d:e8:f3 (RSA)
139/tcp  open  netbios-ssn Samba smbd 3.X - 4.X (workgroup: WORKGROUP)
445/tcp  open  netbios-ssn Samba smbd 3.0.20-Debian (workgroup: WORKGROUP)
3632/tcp open  distccd     distccd v1 ((GNU) 4.2.4 (Ubuntu 4.2.4-1ubuntu4))
Service Info: OSs: Unix, Linux; CPE: cpe:/o:linux:linux_kernel

Host script results:
|_clock-skew: mean: 2h31m25s, deviation: 3h32m08s, median: 1m24s
| smb-os-discovery: 
|   OS: Unix (Samba 3.0.20-Debian)
|   Computer name: lame
|   NetBIOS computer name: 
|   Domain name: hackthebox.gr
|   FQDN: lame.hackthebox.gr
|_  System time: 2020-11-30T02:50:16-05:00
| smb-security-mode: 
|   account_used: <blank>
|   authentication_level: user
|   challenge_response: supported
|_  message_signing: disabled (dangerous, but default)
|_smb2-time: Protocol negotiation failed (SMB2)

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 54.11 seconds
```

Nmap shows that FTP, ssh, and samba services are open on the box.
by viewing to FTP version I know that box is vulnerable to FTP backdoor cmd execution. samba is also run on an older version, there should be a vulnerability for that version.

## FTP

Let's start with FTP. we can log in into FTP using `anonymous` as user and pass. but nothing interested found in FTP.

lets search exploit for `vsftpd 2.3.4` .

![ftpexploit](/assets/htb/lame/ftpexploit.png)

this is a Metasploit script so let's fire up MSF and try this one.

![ftp failed](/assets/htb/lame/ftpfail.png)

this exploit is failed, don't know why, we will see when we get into the box. lets move on to next service.

## SMB

we have samba service lets enumerate it. first, we try to find out share on smb.

`smbclient -L //10.10.10.3`

![smbshare](/assets/htb/lame/smbshare.png)

we got `/tmp` and `/opt` share. tried to access `opt` but it needs a password.

but we can access `tmp` share. nothing seems to be interested in this share. Let's check the exploit for smb version. after a bit of search, I found an exploit.

[usermap exploit](https://www.exploit-db.com/exploits/16320)

there is also a Metasploit script for this exploit. Let's try out that.

## Getting root

![rootshell](/assets/htb/lame/rootshell.png)

by using the usermap script exploit we get a root shell on the box.
Let's see how this exploit works by viewing source code.

![exworking](/assets/htb/lame/exworking.png)

when we send a username with shell meta character we get arbitrary cmd injection this means we can craft and after `nohup` we can add our reverse shell code to get shell.




