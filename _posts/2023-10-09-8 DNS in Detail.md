---
title: 8 DNS in Detail
date: 2023-10-09 11:11:11 +/-TTTT
categories: [Notes, PreSecurity]
tags: [dns, subdomain] 
author: 1  
description: Domain Name System (DNS) explains how it translates domain names into IP addresses, the structure of domain hierarchy, different DNS record types, and the step-by-step process of making a DNS request.    
---

## What is DNS

DNS translates human-readable domain names into numerical IP addresses, making it easier to access devices on the internet.
**IP Addresses:**
- Unique addresses assigned to each computer on the internet.
- Format: Four sets of digits ranging from 0 to 255, separated by periods.
- Example: 104.26.10.229
**Domain Names:**
- Human-readable names associated with IP addresses.
- Example: tryhackme.com
**Benefits of DNS:**
- Allows users to access online resources using easy-to-remember domain names.
- Simplifies communication on the internet by eliminating the need to memorize complex IP addresses.

## Domain Hierarchy

### TLD (Top-Level Domain)

- TLD is the last part of a domain name (e.g., .com in tryhackme.com)

### Types of TLDs

**gTLD (Generic Top Level):** Indicates the purpose of the domain (e.g., .com for commercial, .org for organization)

**ccTLD (Country Code Top Level):** Indicates the geographical location (e.g., .ca for Canada, .co.uk for United Kingdom)
**Note:** There has been an influx of new gTLDs available, such as .online, .club, .website, .biz, etc.

### Second-Level Domain:

- The part of the domain name directly to the left of the TLD (e.g., tryhackme in tryhackme.com) Limited to 63 characters (plus TLD) * Can only use letters (a-z), numbers (0-9), and hyphens (cannot start or end with hyphens)

### Subdomain :

- The part of the domain name to the left of the second-level domain (e.g., admin in admin.tryhackme.com)
- Same creation restrictions as second-level domain (63 characters, a-z, 0-9, hyphens)
- Can be used in combination with periods to create longer names (e.g., jupiter.servers.tryhackme.com)
- Total length must be 253 characters or less
- No limit on the number of subdomains that can be created

## Record Types

DNS isn’t just for websites though, and multiple types of DNS record exist. We’ll go over some of the most common ones that you’re likely to come across.

**A Record** : These records resolve to IPv4 addresses, for example 104.26.10.229

**AAAA Record** : These records resolve to IPv6 addresses, for example 2606:4700:20::681a:be5

**CNAME Record** : These records resolve to another domain name, for example, TryHackMe’s online shop has the subdomain name store.tryhackme.com which returns a CNAME record shops.shopify.com. Another DNS request would then be made to shops.shopify.com to work out the IP address.

**MX Record** : These records resolve to the address of the servers that handle the email for the domain you are querying, for example an MX record response for tryhackme.com would look something like alt1.aspmx.l.google.com. These records also come with a priority flag. This tells the client in which order to try the servers, this is perfect for if the main server goes down and email needs to be sent to a backup server.

**TXT Record** : TXT records are free text fields where any text-based data can be stored. TXT records have multiple uses, but some common ones can be to list servers that have the authority to send an email on behalf of the domain (this can help in the battle against spam and spoofed email). They can also be used to verify ownership of the domain name when signing up for third party services.

## Making a Request

1. You make request to website
2. computer check its local cache if found ip then it stop otherwise it proceed.
3. if it does not find in local cache req is send to `resolver server` . many ISP maintain their own resolver server as well as google and opendns also have their resolver server.
    1. resolver check their cache memory for ip adddress if not found send to :
4. Root Servers
    1. top or the root of dns hierarchy
    2. 13 set of root servers
    3. root server does not know the ip address but it know where to send `resolver server` to find ip adddres.
    4. it redirected `resolver server` to TLD.
5. TLD (Top Level Domain)
    1. store info for top-level domain. eg : .com .net .org etc.
    2. it also does not know the ip adddress. it redirect the `resolver server` to `Authoritive name server`
6. Authorative name server.
    1. It has record for every domain

![Making a request](/assets/img/notes/presecurity/makingrequest.png)

