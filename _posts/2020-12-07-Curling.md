---
title: Curling
date: 2020-12-07 11:15:15 +/-TTTT
categories: [CTF, hackthebox]
tags: [joomla, cron, hexdump]
---

![info card](/assets/htb/curling/infocard.png)

Curling is an easy box, which has a Joomla cms blog. we have to find the username and password on site. we did privs by using cron jobs.

like every box we start with Nmap scan

```
Starting Nmap 7.80 ( https://nmap.org ) at 2020-12-07 02:37 EST
Nmap scan report for 10.10.10.150
Host is up (0.23s latency).

PORT   STATE SERVICE VERSION
22/tcp open  ssh     OpenSSH 7.6p1 Ubuntu 4 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   2048 8a:d1:69:b4:90:20:3e:a7:b6:54:01:eb:68:30:3a:ca (RSA)
|   256 9f:0b:c2:b2:0b:ad:8f:a1:4e:0b:f6:33:79:ef:fb:43 (ECDSA)
|_  256 c1:2a:35:44:30:0c:5b:56:6a:3f:a5:cc:64:66:d9:a9 (ED25519)
80/tcp open  http    Apache httpd 2.4.29 ((Ubuntu))
|_http-generator: Joomla! - Open Source Content Management
|_http-server-header: Apache/2.4.29 (Ubuntu)
|_http-title: Home
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 15.24 seconds
```
we have a webpage and ssh lets take look at the webpage.

![homepage](/assets/htb/curling/homepage.png)

we have a blog site. there is a login form. test sqli and some common user:pass but failed.

by testing index page we found that the site uses PHP. 

by viewing source as well as by viewing Nmap result I found that it uses Joomla

![joomla](/assets/htb/curling/joomla.png)

by using ffuf we got a bunch of directories.

![ffuf](/assets/htb/curling/ffuf.png)

`administrator` has a default Joomla login page.

![administrator](/assets/htb/curling/adminpage.png)

in the one of the blog post we got author name:
`Floris` 

![founduser](/assets/htb/curling/founduser.png)

at the bottom of source code I see comment

`<!-- secret.txt -->`

in `secret.txt` we got some gibberish text.

`Q3VybGluZzIwMTgh`

it looks like base64. after decoding we get:

`Curling2018!`

we can log in as `floris`:`Curling2018!` on the Joomla administration page.

next, we try to upload the PHP shell.

we upload shell in Extention >> template >> Beez3.

![shell](/assets/htb/curling/shell.png)

we get shell as `www-data` user.txt is in floris home directory but we can't access it. we have escalated our privileges.

in the Floris home directory, there is the file `password_backup` which looks like hexdump.

```
cat password_backup
00000000: 425a 6839 3141 5926 5359 819b bb48 0000  BZh91AY&SY...H..
00000010: 17ff fffc 41cf 05f9 5029 6176 61cc 3a34  ....A...P)ava.:4
00000020: 4edc cccc 6e11 5400 23ab 4025 f802 1960  N...n.T.#.@%...`
00000030: 2018 0ca0 0092 1c7a 8340 0000 0000 0000   ......z.@......
00000040: 0680 6988 3468 6469 89a6 d439 ea68 c800  ..i.4hdi...9.h..
00000050: 000f 51a0 0064 681a 069e a190 0000 0034  ..Q..dh........4
00000060: 6900 0781 3501 6e18 c2d7 8c98 874a 13a0  i...5.n......J..
00000070: 0868 ae19 c02a b0c1 7d79 2ec2 3c7e 9d78  .h...*..}y..<~.x
00000080: f53e 0809 f073 5654 c27a 4886 dfa2 e931  .>...sVT.zH....1
00000090: c856 921b 1221 3385 6046 a2dd c173 0d22  .V...!3.`F...s."
000000a0: b996 6ed4 0cdb 8737 6a3a 58ea 6411 5290  ..n....7j:X.d.R.
000000b0: ad6b b12f 0813 8120 8205 a5f5 2970 c503  .k./... ....)p..
000000c0: 37db ab3b e000 ef85 f439 a414 8850 1843  7..;.....9...P.C
000000d0: 8259 be50 0986 1e48 42d5 13ea 1c2a 098c  .Y.P...HB....*..
000000e0: 8a47 ab1d 20a7 5540 72ff 1772 4538 5090  .G.. .U@r..rE8P.
000000f0: 819b bb48                                ...H
```
by the `BZh91AY&SY` we know that it's a bzip2 file. Let's first put it back into binary format.

`xxd -r password_backup > newfile`

![firstbzip](/assets/htb/curling/1st_bz.png)

now it's bzip2 format. let's decompress it by `bzip2 -d newfile`

we get another `gzip` 

![gzip](/assets/htb/curling/1st_gzip.png)

after decompressing for decade we got password. `5d<wdCbdZu)|hChXll`

![passfile](/assets/htb/curling/passfile.png)

lets ssh in Floris and get `user.txt`

![user](/assets/htb/curling/user.png)

## Getting root

in Floris home directory we see folder `admin-area` which has two files. `input` and `report` and the timestamp of two files keep changes every few minutes.

![adminfile](/assets/htb/curling/admin-area-files.png)

let's see if there any cronjob running as root. 

![cronjob](/assets/htb/curling/cronjob.png)

we got cronjob that takes input from `input` file and sends output in `report` file.

`/bin/sh -c curl -K /home/floris/admin-area/input -o /home/floris/admin-area/report`

we can get `/etc/shadow` and try to crack the password for the root user or we can replace ssh keys. here I just fetch for the `root.txt` file.
we put  `url = "file:///root/root.txt"` in `input` file.

![root](/assets/htb/curling/root.png)
