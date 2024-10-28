---
title: bandit
date: 2020-12-02 20:35 +/-TTTT
categories: [CTF, wargame]
tags: [linux]
---

ssh in : `bandit.labs.overthewire.org` port 2220

## level 0 : 
user : bandit0
pass : bandit0

## level 1 : 

pass is stored in readme.use cat to get password 

boJ9jbbUNNfktd78OOpsqOltutMc3MY1

## level 2 :
The password for the next level is stored in a file called - located in the home directory

cat ./-

CV1DtqXWVFXTvM2F0k09SHz0YwRINYA9

## level 3 :

The password for the next level is stored in a file called spaces in this filename located in the home directory

cat spaces\ in\ this\ filename

UmHadQclWmgdLOKQ3YNgjWxGoRMb5luK

## level 4 :

The password for the next level is stored in a hidden file in the inhere directory.

ls -la
cat .hidden

pIwrPrtPN36QITSp3EQaw936yaFoFgAB

## level 5 :

The password for the next level is stored in the only human-readable file in the inhere directory.

find . -type f | xargs file | grep text

koReBOKuIDDepwhWk7jZC0RTdopnAYKh

## level 6 :

The password for the next level is stored in a file somewhere under the inhere directory and has all of the following properties:

	human-readable
    1033 bytes in size
    not executable

find . -size 1033c ! -executable

DXjZPULLxYr17uwoI01bNLQbtFemEgo7

## level 7 :

The password for the next level is stored somewhere on the server and has all of the following properties:

    owned by user bandit7
    owned by group bandit6
    33 bytes in size

find / -type f -user bandit7 -group bandit6 -size 33c 2>/dev/null

HKBPTKQnIay4Fw76bEy8PVxKEDQRKTzs

## level 8 :

The password for the next level is stored in the file data.txt next to the word millionth

cat data.txt | grep millionth

cvX2JJa4CFALtqS87jk27qwqGhBM9plV

## level 9 :

The password for the next level is stored in the file data.txt and is the only line of text that occurs only once

`sort data.txt | uniq -u`

> UsvVyFSfZZWbi6wgC7dAFyFuR6jQQUhR

## level 10 :

The password for the next level is stored in the file data.txt in one of the few human-readable strings, preceded by several ‘=’ characters.

` strings data.txt`

> truKLdjsbJ5g7yyJ2X2R0o3a5HQJFuLk

## level 11 : 

The password for the next level is stored in the file data.txt, which contains base64 encoded data

`cat data.txt | base64 -d`

> IFukwKGsFW8MOq3IRFqrxE1hxTNEbUPR

## level 12 :

The password for the next level is stored in the file data.txt, where all lowercase (a-z) and uppercase (A-Z) letters have been rotated by 13 positions

used online decrypter

> 5Te8Y4drgCRfCx8ugdwuEX8KFC6k2EUu

## level 13 :

The password for the next level is stored in the file data.txt, which is a hexdump of a file that has been repeatedly compressed. For this level it may be useful to create a directory under /tmp in which you can work using mkdir. For example: mkdir /tmp/myname123. Then copy the datafile using cp, and rename it using mv (read the manpages!)

`xxd -r password_backup > newfile` used to put hexdemp to its original binary format (which is bzip2) .

after umcompress ton of file get password

> 8ZjyCRiBWFYkneahHwxCv3wb2a1ORpYL

## level 14 :

The password for the next level is stored in /etc/bandit_pass/bandit14 and can only be read by user bandit14. For this level, you don’t get the next password, but you get a private SSH key that can be used to log into the next level. Note: localhost is a hostname that refers to the machine you are working on

in bandit13 homepage there is private ssh key for bandit14. 
using that key login into bandit14.

```
ssh bandit14@localhost -i sshkey.private
```

## level 15 :

The password for the next level can be retrieved by submitting the password of the current level to port 30000 on localhost.

by privious challenge we know that `bandit14` pass is stored in `/etc/bandit_pass/bandit14`. 

> bandit14 : 4wcYUJFw0k0XLShlDzztnTBHiqxU3b3e

connect to port 30000 using `netcat`

```
nc localhost 30000
4wcYUJFw0k0XLShlDzztnTBHiqxU3b3e //enter bandit14 password
Correct!
BfMYroe26WYalil77FoDi9qh59eK5xNr // got bandit15 password.
```
> BfMYroe26WYalil77FoDi9qh59eK5xNr

## level 16 :

The password for the next level can be retrieved by submitting the password of the current level to port 30001 on localhost using SSL encryption.

use openssl to establish the connection.

```bash
openssl s_client -connect localhost:30001
BfMYroe26WYalil77FoDi9qh59eK5xNr //entered bandit15 password.
Correct!
cluFn7wTiGryunymYOu4RcffSxQluehd // got bandit16 password
```
> cluFn7wTiGryunymYOu4RcffSxQluehd

## level 17 : 

The credentials for the next level can be retrieved by submitting the password of the current level to a port on localhost in the range 31000 to 32000. First find out which of these ports have a server listening on them. Then find out which of those speak SSL and which don’t. There is only 1 server that will give the next credentials, the others will simply send back to you whatever you send to it.

scan with nmap.

```bash
Starting Nmap 7.40 ( https://nmap.org ) at 2020-12-23 14:09 CET                                                                                                                
Nmap scan report for localhost (127.0.0.1)                
Host is up (0.00032s latency).                            
Not shown: 996 closed ports                                                                                                                 
PORT      STATE SERVICE     VERSION                                   
31046/tcp open  echo                                                                                                                                        
31518/tcp open  ssl/echo                                              
| ssl-cert: Subject: commonName=localhost                             
| Subject Alternative Name: DNS:localhost                                                                                                   
| Not valid before: 2020-11-04T15:24:27                               
|_Not valid after:  2021-11-04T15:24:27                                                                                                     
|_ssl-date: TLS randomness does not represent time                                                                                          
31691/tcp open  echo                                                                                                                        
31790/tcp open  ssl/unknown                                                                                                                 
| fingerprint-strings:                                                                                                                                      
|   FourOhFourRequest, GenericLines, GetRequest, HTTPOptions, Help, Kerberos, LDAPSearchReq, LPDString, RTSPRequest, SIPOptions, SSLSessionReq, TLSSessionReq: 
|_    Wrong! Please enter the correct current password                                                                                      
| ssl-cert: Subject: commonName=localhost                                                                                                   
| Subject Alternative Name: DNS:localhost                                                                                                   
| Not valid before: 2020-12-03T12:25:02                                                                                                     
|_Not valid after:  2021-12-03T12:25:02                                                                                                     
|_ssl-date: TLS randomness does not represent time                                                                                          
31960/tcp open  echo                                                                                                                        
1 service unrecognized despite returning data. If you know the service/version, please submit the following fingerprint at https://nmap.org/cgi-bin/submit.cgi?new-service :
SF-Port31790-TCP:V=7.40%T=SSL%I=7%D=12/23%Time=5FE341A4%P=x86_64-pc-linux-                                                                 
SF:gnu%r(GenericLines,31,"Wrong!\x20Please\x20enter\x20the\x20correct\x20c                                                                  
SF:urrent\x20password\n")%r(GetRequest,31,"Wrong!\x20Please\x20enter\x20th                                                                                  
SF:e\x20correct\x20current\x20password\n")%r(HTTPOptions,31,"Wrong!\x20Ple                                                                                  
SF:ase\x20enter\x20the\x20correct\x20current\x20password\n")%r(RTSPRequest                                                                                                     
SF:,31,"Wrong!\x20Please\x20enter\x20the\x20correct\x20current\x20password                                                                                                     
SF:\n")%r(Help,31,"Wrong!\x20Please\x20enter\x20the\x20correct\x20current\                                                                                                     
SF:x20password\n")%r(SSLSessionReq,31,"Wrong!\x20Please\x20enter\x20the\x2                                                                                                     
SF:0correct\x20current\x20password\n")%r(TLSSessionReq,31,"Wrong!\x20Pleas                                                                                                     
SF:e\x20enter\x20the\x20correct\x20current\x20password\n")%r(Kerberos,31,"                                                                                                     
SF:Wrong!\x20Please\x20enter\x20the\x20correct\x20current\x20password\n")%                                                                                                     
SF:r(FourOhFourRequest,31,"Wrong!\x20Please\x20enter\x20the\x20correct\x20                                                                                                     
SF:current\x20password\n")%r(LPDString,31,"Wrong!\x20Please\x20enter\x20th                                                                                                     
SF:e\x20correct\x20current\x20password\n")%r(LDAPSearchReq,31,"Wrong!\x20P                                                                                                     
SF:lease\x20enter\x20the\x20correct\x20current\x20password\n")%r(SIPOption                                                                                                     
SF:s,31,"Wrong!\x20Please\x20enter\x20the\x20correct\x20current\x20passwor                                                                                                     
SF:d\n");                                  

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .                                                                                 
Nmap done: 1 IP address (1 host up) scanned in 89.10 seconds
```

nmap show that only port 31518 and 31790 has ssl encryption.

use openssl to check connection.
port 31518 send back whatever we send. port 31790 give us ssh private key for nex level.
```bash 
openssl s_client -connect localhost:31790
```

```
-----BEGIN RSA PRIVATE KEY-----
MIIEogIBAAKCAQEAvmOkuifmMg6HL2YPIOjon6iWfbp7c3jx34YkYWqUH57SUdyJ
imZzeyGC0gtZPGujUSxiJSWI/oTqexh+cAMTSMlOJf7+BrJObArnxd9Y7YT2bRPQ
Ja6Lzb558YW3FZl87ORiO+rW4LCDCNd2lUvLE/GL2GWyuKN0K5iCd5TbtJzEkQTu
DSt2mcNn4rhAL+JFr56o4T6z8WWAW18BR6yGrMq7Q/kALHYW3OekePQAzL0VUYbW
JGTi65CxbCnzc/w4+mqQyvmzpWtMAzJTzAzQxNbkR2MBGySxDLrjg0LWN6sK7wNX
x0YVztz/zbIkPjfkU1jHS+9EbVNj+D1XFOJuaQIDAQABAoIBABagpxpM1aoLWfvD
KHcj10nqcoBc4oE11aFYQwik7xfW+24pRNuDE6SFthOar69jp5RlLwD1NhPx3iBl
J9nOM8OJ0VToum43UOS8YxF8WwhXriYGnc1sskbwpXOUDc9uX4+UESzH22P29ovd
d8WErY0gPxun8pbJLmxkAtWNhpMvfe0050vk9TL5wqbu9AlbssgTcCXkMQnPw9nC
YNN6DDP2lbcBrvgT9YCNL6C+ZKufD52yOQ9qOkwFTEQpjtF4uNtJom+asvlpmS8A
vLY9r60wYSvmZhNqBUrj7lyCtXMIu1kkd4w7F77k+DjHoAXyxcUp1DGL51sOmama
+TOWWgECgYEA8JtPxP0GRJ+IQkX262jM3dEIkza8ky5moIwUqYdsx0NxHgRRhORT
8c8hAuRBb2G82so8vUHk/fur85OEfc9TncnCY2crpoqsghifKLxrLgtT+qDpfZnx
SatLdt8GfQ85yA7hnWWJ2MxF3NaeSDm75Lsm+tBbAiyc9P2jGRNtMSkCgYEAypHd
HCctNi/FwjulhttFx/rHYKhLidZDFYeiE/v45bN4yFm8x7R/b0iE7KaszX+Exdvt
SghaTdcG0Knyw1bpJVyusavPzpaJMjdJ6tcFhVAbAjm7enCIvGCSx+X3l5SiWg0A
R57hJglezIiVjv3aGwHwvlZvtszK6zV6oXFAu0ECgYAbjo46T4hyP5tJi93V5HDi
Ttiek7xRVxUl+iU7rWkGAXFpMLFteQEsRr7PJ/lemmEY5eTDAFMLy9FL2m9oQWCg
R8VdwSk8r9FGLS+9aKcV5PI/WEKlwgXinB3OhYimtiG2Cg5JCqIZFHxD6MjEGOiu
L8ktHMPvodBwNsSBULpG0QKBgBAplTfC1HOnWiMGOU3KPwYWt0O6CdTkmJOmL8Ni
blh9elyZ9FsGxsgtRBXRsqXuz7wtsQAgLHxbdLq/ZJQ7YfzOKU4ZxEnabvXnvWkU
YOdjHdSOoKvDQNWu6ucyLRAWFuISeXw9a/9p7ftpxm0TSgyvmfLF2MIAEwyzRqaM
77pBAoGAMmjmIJdjp+Ez8duyn3ieo36yrttF5NSsJLAbxFpdlc1gvtGCWW+9Cq0b
dxviW8+TFVEBl1O4f7HVm6EpTscdDxU+bCXWkfjuRb7Dy9GOtt9JPsX8MBTakzh3
vBgsyi/sN3RqRBcGU40fOoZyfAMT8s1m/uYv52O6IgeuZ/ujbjY=
-----END RSA PRIVATE KEY-----
```

## level 18

There are 2 files in the homedirectory: passwords.old and passwords.new. The password for the next level is in passwords.new and is the only line that has been changed between passwords.old and passwords.new

we use `diff` cmd which compare files line by line and show differen line in both files.

```bash
diff password.new password.old
```
> kfBf3eYk5BPBRzwjqutbbfE887SVc5Yd


## level 19

The password for the next level is stored in a file readme in the homedirectory. Unfortunately, someone has modified .bashrc to log you out when you log in with SSH.

we can't log in using password. but we can execute cmd using ssh.

```bash
bandit18@bandit.labs.overthewire.org -p 2220 cat /home/bandit18/readme
```
> IueksS7Ubh8G3DCwVzrTd8rAVOwq3M5x

## level 20

To gain access to the next level, you should use the setuid binary in the homedirectory. Execute it without arguments to find out how to use it. The password for this level can be found in the usual place (/etc/bandit_pass), after you have used the setuid binary.

in home dir we got `bandit20-do` suid.
suid bit is used to run cmd as other user.
here we can run cmd as `bandit20`

```bash
./badnit20-do cat /etc/bandit_pass/bandit20 
```

> GbKksEFF4yrVs6il55v6gwY5aVje5f0j

## level 21

There is a setuid binary in the homedirectory that does the following: it makes a connection to localhost on the port you specify as a commandline argument. It then reads a line of text from the connection and compares it to the password in the previous level (bandit20). If the password is correct, it will transmit the password for the next level (bandit21).

the `suconnect` is setuid binary. which run as other user. here we can run it as `bandit21`. this binary connect with given port and read for `bandit20` password. then it see if input password and bandit20 password is match or not. if matches it send us `bandit21` password.

so here we first start TCP server on port 1337.

```bash
echo "GbKksEFF4yrVs6il55v6gwY5aVje5f0j" | nc -l localhost -p 1337 &
```
it will run in background.

next we establish connection using `suconnect `

```bash
./suconnect 1337
```
it will connect to port 1337 and read bandit20 password. 
then it matches that with bandit20 password if correct then it give us bandit21 password.

> gE269g2h3mw3pwgrj0Ha9Uoqen1c9DGr


## level 22

A program is running automatically at regular intervals from cron, the time-based job scheduler. Look in /etc/cron.d/ for the configuration and see what command is being executed.

here are some cron jobs running. lets take look at it.
first we list out cronjobs in `/etc/cron.d/`

```bash
bandit21@bandit:/etc/cron.d$ ls -la
total 36
drwxr-xr-x  2 root root 4096 Jul 11 15:56 .
drwxr-xr-x 87 root root 4096 May 14  2020 ..
-rw-r--r--  1 root root   62 May 14  2020 cronjob_bandit15_root
-rw-r--r--  1 root root   62 Jul 11 15:56 cronjob_bandit17_root
-rw-r--r--  1 root root  120 May  7  2020 cronjob_bandit22
-rw-r--r--  1 root root  122 May  7  2020 cronjob_bandit23
-rw-r--r--  1 root root  120 May 14  2020 cronjob_bandit24
-rw-r--r--  1 root root   62 May 14  2020 cronjob_bandit25_root
-rw-r--r--  1 root root  102 Oct  7  2017 .placeholder
```

we interested in `bandit22` password. so lets take look at `bandit22` cronjob.

```bash
bandit21@bandit:/etc/cron.d$ cat cronjob_bandit22
@reboot bandit22 /usr/bin/cronjob_bandit22.sh &> /dev/null
* * * * * bandit22 /usr/bin/cronjob_bandit22.sh &> /dev/null
```

cronjob is running `/usr/bin/cronjob_bandit22.sh` script for every minute.

lets take look at `cronjob_bandit22.sh`

```bash
bandit21@bandit:/etc/cron.d$ cat /usr/bin/cronjob_bandit22.sh 
#!/bin/bash
chmod 644 /tmp/t7O6lds9S0RqQh9aMcz6ShpAoZKF7fgv
cat /etc/bandit_pass/bandit22 > /tmp/t7O6lds9S0RqQh9aMcz6ShpAoZKF7fgv
```
this script read password from `/etc/bandit_pass/bandit22` and save to file `t7O6lds9S0RqQh9aMcz6ShpAoZKF7fgv` in `/tmp` directory.

we can get password. 

```bash
cat /tmp/t7O6lds9S0RqQh9aMcz6ShpAoZKF7fgv
```

> Yk7owGAcWjwMVRwrTesJEwB7WVOiILLI


## level 23

A program is running automatically at regular intervals from cron, the time-based job scheduler. Look in /etc/cron.d/ for the configuration and see what command is being executed.

first we analyze the cronjob.

```bash
bandit22@bandit:/etc/cron.d$ cat cronjob_bandit23
@reboot bandit23 /usr/bin/cronjob_bandit23.sh  &> /dev/null
* * * * * bandit23 /usr/bin/cronjob_bandit23.sh  &> /dev/null
```

this cronjob run `/usr/bin/cronjob_bandit23.sh` for every minute.

when we cat `cronjob_bandit23.sh` script.

```sh
#!/bin/bash

myname=$(whoami)
mytarget=$(echo I am user $myname | md5sum | cut -d ' ' -f 1)

echo "Copying passwordfile /etc/bandit_pass/$myname to /tmp/$mytarget"

cat /etc/bandit_pass/$myname > /tmp/$mytarget
```

on first line it run `whoami` cmd and assign output to `myname` variable.

next it define another `mytarget` variable. and asign to output of

`echo I am user $myname | md5sum | cut -d ' ' -f 1`

so basically it convert "I am user bandit22" to md5 hash. and `cut` use to get first part of md5hash.

`cut` is used to extract data. here it extract first field.

when we locally run this cmd we get `$mytarget`

```bash
bandit22@bandit:/etc/cron.d$ echo "I am user bandit23" | md5sum | cut -d ' ' -f 1
8ca319486bfbbc3663ea0fbe81326349
```

and in `/tmp/8ca319486bfbbc3663ea0fbe81326349` we have our password.

```bash
bandit22@bandit:/etc/cron.d$ cat /tmp/8ca319486bfbbc3663ea0fbe81326349
jc1udXuA1tiHqjIsL8yaapX5XIAI6i0n
```


> jc1udXuA1tiHqjIsL8yaapX5XIAI6i0n


## level 24

A program is running automatically at regular intervals from cron, the time-based job scheduler. Look in /etc/cron.d/ for the configuration and see what command is being executed.

first we see cronjob.

```bash
bandit23@bandit:/etc/cron.d$ cat cronjob_bandit24
@reboot bandit24 /usr/bin/cronjob_bandit24.sh &> /dev/null
* * * * * bandit24 /usr/bin/cronjob_bandit24.sh &> /dev/null
```
it runs script `/usr/bin/cronjob_bandit24.sh` for every one minutes.

Lets take look at script.

```sh
#!/bin/bash

myname=$(whoami)

cd /var/spool/$myname
echo "Executing and deleting all scripts in /var/spool/$myname:"
for i in * .*;
do
    if [ "$i" != "." -a "$i" != ".." ];
    then
        echo "Handling $i"
        owner="$(stat --format "%U" ./$i)"
        if [ "${owner}" = "bandit23" ]; then
            timeout -s 9 60 ./$i
        fi
        rm -f ./$i
    fi
done
```

```bash
myname=$(whoami)

cd /var/spool/$myname
```
run `whoami` cmd and output asign to `myname` variable. then it change the directory to `/var/spool/bandit24`.

```bash
for i in * .*;
do
    if [ "$i" != "." -a "$i" != ".." ];
    then
        echo "Handling $i"
        owner="$(stat --format "%U" ./$i)"
        if [ "${owner}" = "bandit23" ]; then
            timeout -s 9 60 ./$i
        fi
        rm -f ./$i
    fi
done
```

next it run for loop for each file in `/var/spool/bandit24` 

first it exclude `.`  and `..` from `/var/spool/bandit24`.

then it echo `handling filename`

next it checks for owner of file and if owner of file is `bandit23` it run that script for 60 seconds and then kill it. (9 is for `SIGKILL`)

*timeout : allows you to run a command with a time limit.*

and lastly it remove script from directory.

Now we have write permisson on `/var/spool/bandit24` so we can create script which give us password of `bandit24` and place it in `/var/spool/bandit24`.

*script*
```bash
#!/bin/bash

cat /etc/bandit_pass/bandit24 > /tmp/passfile
```
save this file as `new.sh` to `/var/spool/bandit24` and give excute permission.

```bash
chmod 777 new.sh
```
create file in `tmp` dir : `passfile`
```bash
touch /tmp/passfile 
```

give it permission to write anyone.

```bash
chmod 777 /tmp/passfile 
```

wait for one minut and we get `bandit24` password in `/tmp/passfile`

```bash
bandit23@bandit:/etc/cron.d$ cat /tmp/passfile
UoMYTrfrBFHyQXmg6gzctqAwOmw1IohZ
```

> UoMYTrfrBFHyQXmg6gzctqAwOmw1IohZ

## level 25

A daemon is listening on port 30002 and will give you the password for bandit25 if given the password for bandit24 and a secret numeric 4-digit pincode. There is no way to retrieve the pincode except by going through all of the 10000 combinations, called brute-forcing.

To solve this challenge we write a script that connect to port 30002 and enter bandit25 password and pincode.

```bash
nc localhost 30002 <<< $(for a in $(seq 0000 9999);do echo “UoMYTrfrBFHyQXmg6gzctqAwOmw1IohZ $a”;done)
```

first we run for loop for 0000 to 9999 and for each iteration we test pincode.

> uNG9O58gUE7snukf3bvZ0rxhtnjzSGzG




