---
title: 6 Linux Hardening
date: 2022-08-22 11:11:11 +/-TTTT
categories: [Notes, Linux]
tags: [hardening, selinux, apparmor] 
author: 1  
description: This guide emphasizes keeping Linux systems updated and explores additional security options like SELinux, AppArmor, and TCP wrappers for restricted access control.
---

## Linux security

- keep os up to date.

```bash
cyber7ron@htb[/htb]$ apt update && apt dist-upgrade
```

- An option for further locking down Linux systems is `Security-Enhanced Linux` (`SELinux`) or `AppArmor`.

**TCP Wrappers**

- is a security mechanism used in Linux systems that allows the system administrator to control which services are allowed access to the system.
- It check IP address of client and if ip is in the tcp wrappers config file it will give access to client.
- TCP Wrappers use the following configuration files :
	- `/etc/hosts.allow` :  specifies which services and hosts are allowed access to the system
	-  `/etc/hosts.deny` : specifies which services and hosts are not allowed access.


```shell-session
cyber7ron@htb[/htb]$ cat /etc/hosts.allow

# Allow access to SSH from the local network
sshd : 10.129.14.0/24

# Allow access to FTP from a specific host
ftpd : 10.129.14.10

# Allow access to Telnet from any host in the inlanefreight.local domain
telnetd : .inlanefreight.local
```

```shell-session
cyber7ron@htb[/htb]$ cat /etc/hosts.deny

# Deny access to all services from any host in the inlanefreight.com domain
ALL : .inlanefreight.com

# Deny access to SSH from a specific host
sshd : 10.129.22.22

# Deny access to FTP from hosts with IP addresses in the range of 10.129.22.0 to 10.129.22.255
ftpd : 10.129.22.0/24
```

> It is important to remember that the order of the rules in the files is important. The first rule that matches the requested service and host is the one that will be applied. It is also important to note that TCP wrappers are not a replacement for a firewall, as they are limited by the fact that they can only control access to services and not to ports.

***

## Firewall Setup

- Firewall provide security by controlling and monitoring network traffic between different network segment. eg : internal , external or different network zones.
- The iptables utility provided a simple yet powerful command-line interface for configuring firewall rules, which could be used to filter traffic based on various criteria such as IP addresses, ports, protocols, and more.
- In linux firewall functionality is implemented ussing `Netfilter framework` which is internal part of the kernel.

#### Iptables

- set of rules for filtering network traffic based on various criteria such as source & destination IP ,port, protocols, and more.
- There also exist other solutions like `nftables`, `ufw` and `firewalld`.
	- `nftables` modern syntax and improvised performance over iptables. but the syntax of nftables rules not compatible with iptables so migration to nftables requires some efforts.
	- `UFW` "Uncomplicated Firewall" ,  UFW is built on top of the iptables framework like nftables and provides an easier way to manage firewall rules.
	- `FirewallD` used to manage complex firewall configurations, and it supports a rich set of rules for filtering network traffic and can be used to create custom firewall zones and services

- Main Component of iptables is 

| **Component** | **Description**                                                                                                                                                              |
| ------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `Tables`      | Tables are used to organize and categorize firewall rules.                                                                                                                   |
| `Chains`      | Chains are used to group a set of firewall rules applied to a specific type of network traffic.                                                                              |
| `Rules`       | Rules define the criteria for filtering network traffic and the actions to take for packets that match the criteria.                                                         |
| `Matches`     | Matches are used to match specific criteria for filtering network traffic, such as source or destination IP addresses, ports, protocols, and more.                           |
| `Targets`     | Targets specify the action for packets that match a specific rule. For example, targets can be used to accept, drop, or reject packets or modify the packets in another way. |

**tables** :

- tables are used to organize and categorize firewall rules. Each table is responsible for performing a specific set of tasks.

|**Table Name**|**Description**|**Built-in Chains**|
|---|---|---|
|`filter`|Used to filter network traffic based on IP addresses, ports, and protocols.|INPUT, OUTPUT, FORWARD|
|`nat`|Used to modify the source or destination IP addresses of network packets.|PREROUTING, POSTROUTING|
|`mangle`|Used to modify the header fields of network packets.|PREROUTING, OUTPUT, INPUT, FORWARD, POSTROUTING|

- iptables provides a fourth table called the raw table, which is used to configure special packet processing options. 

- The raw table contains two built-in chains: PREROUTING and OUTPUT.

**Chains** :

- chains organize rules that define how network traffic should be filtered or modified.
- two types of chains in iptables
	- Built-in chains :  
		- pre-defined and automatically created when a table is created. Each table has a different set of built-in chains.
		- `filter` table has three built-in chains. `input, output and forward`. 
		- These chains are used to filter incoming and outgoing network traffic, as well as traffic that is being forwarded between different network interfaces.
	- User-defined chains
		- `User-defined chains` can simplify rule management by grouping firewall rules based on specific criteria, such as source IP address, destination port, or protocol. They can be added to any of the three main tables.


**Rules and Targets** 

- Iptables rules are used to define the criteria for filtering network traffic and the actions to take for packets that match the criteria.
- Rules are added to chains using the `-A` option followed by the chain name

Some of the common targets used in iptables rules include the following:

|**Target Name**|**Description**|
|---|---|
|`ACCEPT`|Allows the packet to pass through the firewall and continue to its destination|
|`DROP`|Drops the packet, effectively blocking it from passing through the firewall|
|`REJECT`|Drops the packet and sends an error message back to the source address, notifying them that the packet was blocked|
|`LOG`|Logs the packet information to the system log|
|`SNAT`|Modifies the source IP address of the packet, typically used for Network Address Translation (NAT) to translate private IP addresses to public IP addresses|
|`DNAT`|Modifies the destination IP address of the packet, typically used for NAT to forward traffic from one IP address to another|
|`MASQUERADE`|Similar to SNAT but used when the source IP address is not fixed, such as in a dynamic IP address scenario|
|`REDIRECT`|Redirects packets to another port or IP address|
|`MARK`|Adds or modifies the Netfilter mark value of the packet, which can be used for advanced routing or other purposes|

EG: we want to add a new entry to the INPUT chain that allows incoming TCP traffic on port 22 (SSH) to be accepted. The command for that would look like the following:

```shell-session
cyber7ron@htb[/htb]$ sudo iptables -A INPUT -p tcp --dport 22 -j ACCEPT
```

**Matches** 

- specify the criteria that determine whether a firewall rule should be applied to a particular packetr or connection.
- Matches are used to match specific characteristics of network traffic, such as the source or destination IP address, protocol, port number, and more.

|**Match Name**|**Description**|
|---|---|
|`-p` or `--protocol`|Specifies the protocol to match (e.g. tcp, udp, icmp)|
|`--dport`|Specifies the destination port to match|
|`--sport`|Specifies the source port to match|
|`-s` or `--source`|Specifies the source IP address to match|
|`-d` or `--destination`|Specifies the destination IP address to match|
|`-m state`|Matches the state of a connection (e.g. NEW, ESTABLISHED, RELATED)|
|`-m multiport`|Matches multiple ports or port ranges|
|`-m tcp`|Matches TCP packets and includes additional TCP-specific options|
|`-m udp`|Matches UDP packets and includes additional UDP-specific options|
|`-m string`|Matches packets that contain a specific string|
|`-m limit`|Matches packets at a specified rate limit|
|`-m conntrack`|Matches packets based on their connection tracking information|
|`-m mark`|Matches packets based on their Netfilter mark value|
|`-m mac`|Matches packets based on their MAC address|
|`-m iprange`|Matches packets based on a range of IP addresses|

#### ToDo

|     |                                                                                                              |
| --- | ------------------------------------------------------------------------------------------------------------ |
| 1.  | Launch a web server on TCP/8080 port on your target and use iptables to block incoming traffic on that port. |
| 2.  | Change iptables rules to allow incoming traffic on the TCP/8080 port.                                        |
| 3.  | Block traffic from a specific IP address.                                                                    |
| 4.  | Allow traffic from a specific IP address.                                                                    |
| 5.  | Block traffic based on protocol.                                                                             |
| 6.  | Allow traffic based on protocol.                                                                             |
| 7.  | Create a new chain.                                                                                          |
| 8.  | Forward traffic to a specific chain.                                                                         |
| 9.  | Delete a specific rule.                                                                                      |
| 10. | List all existing rules.                                                                                     |

***

## System Logs and Monitoring

- there are several different types of system logs on linux.

1. kernel logs
2. system logs
3. authentication logs
4. application logs
5. security logs

#### kernel logs

- contain information about system kernel, including hw drivers , system calls and kernel events.
- stored in `/var/log/kern.log` file.
- provide information on vulnerable or outdated drivers , also provide info about system crashesh,  and other events.

#### System logs

- contain information about system-level events, such as service start and stops, login attempts and system reboots.
- stored in `/var/log/syslog`
- In addition, we can use the `syslog` to identify potential issues that could impact the availability or performance of the system, such as failed service starts or system reboots.

#### Authentication logs

- These logs contain information about user authentication attempts, including successful and failed attempts. 
- They are stored in the `/var/log/auth.log` file. 
- It is important to note that while the `/var/log/syslog` file may contain similar login information, the `/var/log/auth.log` file specifically focuses on user authentication attempts, making it a more valuable resource

#### Application logs

- These logs contain information about the activities of specific applications running on the system. 
- They are often stored in their own files, such as `/var/log/apache2/error.log` for the Apache web server or `/var/log/mysql/error.log` for the MySQL database server. 
- These logs are particularly important when we are targeting specific applications, such as web servers or databases,

#### Security logs

- These security logs and their events are often recorded in a variety of log files, depending on the specific security application or tool in use. For example, the Fail2ban application records failed login attempts in the `/var/log/fail2ban.log` file, while the UFW firewall records activity in the `/var/log/ufw.log` file. 
- Other security-related events, such as changes to system files or settings, may be recorded in more general system logs such as `/var/log/syslog` or `/var/log/auth.log`.

***
