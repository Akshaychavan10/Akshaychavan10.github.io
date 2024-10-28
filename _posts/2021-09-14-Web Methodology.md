---
title: Web Recon
date: 2021-09-13 11:11:11 +/-TTTT
categories: [bug, methodology]
tags: [web]
---

My Simple Reconnaissance Methodology for Bug Hunting.

# Recon

Target.com

***

## Directory fuzzing

- Direcotory fuzzing for files and directory with know extentions. : 

extention : db,php,txt based on tech used.

`ffuf -u $target -w /usr/share/wordlist/usr/share/seclists/Discovery/Web-Content/directory-list-lowercase-2.3-big.txt -e db php txt -of md -o dirscan/big_with_db_php_txt.md`

***

## Subdomains 

https://chaos.projectdiscovery.io/#/ --> keep track of new subdomains and targets.


**Fuzzing**

```
ffuf -w /usr/share/seclists/Discovery/DNS/dns-Jhaddix.txt -u http://target.com/ -H "Host:FUZZ.target.com" -of md -o subdomain/fuzzing_dnsjhaddix.md

```

**Scrapping**

- cert.sh --> subdomain/cert_subs

- burp suit. :

1. add target to scope. 
2. check in `user advanced scope control` (go to target -> scope)
3. host or ip ranges : paypal (target name here)
4. browse like normal user. (turn on auto fill form)
5. now spider all `site-map` traffic


save output :

right click --> enagement tools --> analyse target --> save report as html file.

- javascripts :

see js section below.

- subfinder :

`subfinder -config config.yaml -all -d target.com`

- github subdomain scrapping :

`python3 github-subdomains.py -t github_token -d target.com`

- Cloud ranges :

Watch Sam Erb defcon talk on this.

 
- alteration of subdomains : 


`altdns -i subdomains.txt -o data_output -w words.txt -r -s results_output.txt`

```
- subdomains.txt contains the known subdomains for an organization
- data_output is a file that will contain the massive list of altered and permuted subdomains
- words.txt is your list of words that you'd like to permute your current subdomains with (i.e. admin, staging, dev, qa) one word per line
```

after you gather subds from all sources save it to in one file. --> check if subdomains is working or not.

`cat all_subdomain | httprobe > working_subs`

### Screenshots 

`EyeWitness -f urls.txt --web -d dir_name_to_output`

### js 

- Find js file using waybackurls :

`gau paypal.com |grep -iE '\.js'|grep -ivE '\.json'|sort -u  >> paypalJS.txt`

- You can also collect js file in burp suit.

- get all js file from sub and main domains :

`getJS --complete --input subs.txt`

- next find urls,endpoints and parameter in js files:

`python linkfinder.py -i file.txt -o results.html`

- discover sensitive data like apikeys, accesstoken, authorizations, jwt,..etc in JavaScript files.

`python3 SecretFinder.py -i https://example.com/ or subs.txt -e -g 'jquery;bootstrap;api.google.com'`
`-g` : ignore external libs.

### github

https://github.com/gwen001/github-search

*incomplete section*

## Port scanning :

1. Gather all ip address of subdomains and domains :

`dnmasscan subdomains.lst ip_outputs`

2. remove dups in list.

`sort filename | uniq -u`

3. feed to masscan :

`masscan -p1-65535 -iL $ipFile --max-rate 1800 -oG $outPutFile.log`

4. feed to nmap to scan services.

save output in `-oG output.gnmap` because `brutespray` take this format.

5. feed to `brutespray` to check default creds.

`python brutespray.py --file output.gnmap`


## Misc

### waybackurl

gau :

`cat domains.txt | gau --blacklist png,jpg,gif,css,pdf --o output.txt`

remove duplicates parameter.

`cat gauurl.txt | uro > output.txt`

test for potential vulnerable parameter : 

`cat urooutput.txt | gf potential | sed ‘s/=.*/=/’ | sed ‘s/URL: //’ | tee testxss.txt` //for xss + ssrf + open redirect parameters

`cat gauurl.txt | gf wordpress | sed ‘s/=.*/=/’ | sed ‘s/URL: //’ | tee testxss.txt` // for wordpress urls

*You can send testxss.txt to dalfox to automate xss hunting*



### Subdomain takeover

`git pull nuclei-templates` : update templates.

`cat all_subs.txt | nuclie -t nuclei-templates/takeovers/ -o output_file` // if you want to test all templates in folder.

`cat all_subs.txt | nuclie -t nuclei-templates/takeovers/hatenablog-takeover.yaml -o output_file` // scan single templage.

### Cms identifications

- wappalyzer

- whatweb : `/home/kali/web_pentest/scripts/WhatWeb/whatweb target.com`