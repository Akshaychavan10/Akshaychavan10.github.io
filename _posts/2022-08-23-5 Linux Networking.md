---
title: 5 Linux Networking
date: 2022-08-23 11:11:11 +/-TTTT
categories: [Notes, Linux]
tags: [linux configuration] 
author: 1  
description: Network configuration on Linux for penetration testers, covering interface setup, IP addressing, routing, DNS, and file editing for persistent changes.
---

## Network Configuration
- As a penetration tester, one of the key skills required is configuring and managing network settings on Linux systems. 
- This skill is valuable in setting up testing environments, controlling network traffic, or identifying and exploiting vulnerabilities.
-  we should learn how to configure network interfaces.
	- this includes IP addresses, configuring network devices such as routers and switches, and setting up network protocols.
	- Also Its important to understand networking protocol such as TCP/IP, DNS, DHCP, and FTP.

#### Configuring Network Interfaces 

- we can configure local network interfaces using `ifconfig` or the `ip` cmd.
- `ifconfig` cmd has been deprecated in newer version of linux and replaced by `ip` cmd which offers more advanced features. (`ifconfig` is still widely used by users).

**Activate Network Interface** :

```shell-session
cyber7ron@htb[/htb]$ sudo ifconfig eth0 up     # OR
cyber7ron@htb[/htb]$ sudo ip link set eth0 up
```

**Assign IP Address to an Interface** 

```shell-session
cyber7ron@htb[/htb]$ sudo ifconfig eth0 192.168.1.2
```

**Assign a Netmask to an Interface** 

```shell-session
cyber7ron@htb[/htb]$ sudo ifconfig eth0 netmask 255.255.255.0
```

**Assign the route to an interface**

- When we want to set the default gateway for a network interface, we can use the `route` command with the `add` option.
- his allows us to specify the gateway's IP address and the network interface to which it should be applied
- *what is default gateway* 
	- a default gateway is a device or a node on a network that serves as the access point or the router to other networks or the internet.
	- When a device on a local network wants to communicate with devices on other networks, it sends the data packets to the default gateway, which then forwards the packets to the appropriate destination network.
	- Without a default gateway, devices on a network would only be able to communicate with other devices on the same network.
	- In summary, the default gateway acts as a bridge between local networks and external networks, facilitating communication between them.

```shell-session
cyber7ron@htb[/htb]$ sudo route add default gw 192.168.1.1 eth0
```

**Editing DNS Settings** 

- Its neccessary to set Domain Name System server to ensure proper network functionality.
- The `/etc/resolv.conf` file is a plain text file containing the system's DNS information.
-  any changes made to this file will only apply to the current session and must be updated if the system is restarted or the network configuration is changed

```shell-session
cyber7ron@htb[/htb]$ sudo vim /etc/resolv.conf
```

**Editing Interfaces** 

- After completing all of the above necessary modifications to the network configuration, it is essential to ensure that these changes are saved to persist across reboots.
- This can be achieved by editing the `/etc/network/interfaces` file.

```shell-session
cyber7ron@htb[/htb]$ sudo vim /etc/network/interfaces
```

- We can add the network configuration settings to the file like this

```
auto eth0
iface eth0 inet static
  address 192.168.1.2
  netmask 255.255.255.0
  gateway 192.168.1.1
  dns-nameservers 8.8.8.8 8.8.4.4
```

- after editing this file we must restart the network service to apply the changes.

```shell-session
cyber7ron@htb[/htb]$ sudo systemctl restart networking
```

#### Network Access Control 

- NAC is a security system that ensures that only authorized and compliant devices are granted access to the network, preventing unauthorized access, data breaches, and other security threats.
- The following are the different NAC technologies that can be used to enhance security measures:
	- Discretionary access control (DAC)
	- Mandatory access control (MAC)
	- Role-based access control (RBAC)

**Discretionary Access Control** :

- owner of the  resource can decide who has access to their resources and what actions they are authorized to perform. These permissions can be set for reading, writing, executing, or deleting the resource

**Mandatory Access Control** :

- Each resource is assigned a security label that identifies its security level, and each user or process is assigned a security clearance that identifies its security level. 
- Access to a resource is only granted if the user's or process's security level is equal to or greater than the security level of the resource. 
- MAC is often used in operating systems and applications that require a high level of security, such as military or government systems, financial systems

**Role-based Access Control** :

- RBAC assigns permissions to users based on their roles within an organization.
- Users are assigned roles based on their job responsibilities or other criteria, and each role is granted a set of permissions that determine the actions they can perform.

#### Monitoring

- Network monitoring involves capturing, analyzing, and interpreting network traffic
-  where we use several tools for network monitoring on Linux systems like Ubuntu and Windows systems, like Wireshark, tshark, and Tcpdump.
- For More info take look at `Intro to network traffic analysis` Module in this vault.

#### Troubleshooting

**ping** : test connectivity between two devices. it sends packets to a remote host and measures the time to return them.

```shell-session
cyber7ron@htb[/htb]$ ping <remote_host>
```

**Traceroute** : traces the route packets take to reach a remote host. it sends packets with increasing time-to-live values to a remote host and display the ip addresses of the devices that the packets pass through.

```shell-session
cyber7ron@htb[/htb]$ traceroute www.inlanefreight.com

traceroute to www.inlanefreight.com (134.209.24.248), 30 hops max, 60 byte packets
 1  * * *
 2  10.80.71.5 (10.80.71.5)  2.716 ms  2.700 ms  2.730 ms
 3  * * *
 4  10.80.68.175 (10.80.68.175)  7.147 ms  7.132 ms 10.80.68.161 (10.80.68.161)  7.393 ms
```

- In the above output maximum number of hops allowed is 30. This ensures that the connection is established efficiently and reliably.
- The second line shows the first hop in the traceroute, which is the local network gateway with the IP address 10.80.71.5, followed by the next three columns show the time it took for each of the three packets sent to reach the gateway in milliseconds
- Next, we see the second hop in the traceroute. However, there was no response from the device at that hop, indicated by the three asterisks instead of the IP address. This could mean the device is down, blocking ICMP traffic, or a network issue caused the packets to drop.
- In the fourth line, we can see the third hop in the traceroute, consisting of two devices with IP addresses 10.80.68.175 and 10.80.68.161

**Netstat** : `Netstat` is used to display active network connections and their associated ports.

```shell-session
cyber7ron@htb[/htb]$ netstat -a

Active Internet connections (servers and established)
Proto Recv-Q Send-Q Local Address           Foreign Address         State      
tcp        0      0 localhost:5901          0.0.0.0:*               LISTEN     
tcp        0      0 0.0.0.0:sunrpc          0.0.0.0:*               LISTEN     
tcp        0      0 0.0.0.0:http            0.0.0.0:*               LISTEN     
tcp        0      0 0.0.0.0:ssh             0.0.0.0:*               LISTEN
...SNIP...
```


#### Hardening

- security mechanisms such as SELinux, AppArmor, and TCP wrappers are used to secure linux in organization.  they provide some extra security to linux systems.

**SELinux** 

- SELinux is a security feature built into the Linux kernel that provides a flexible Mandatory Access Control (MAC) system.
- security policies based on security labels assigned to files, processes, and network resources.
**AppArmor** :
- AppArmor is a Linux security module that supplements the traditional Linux Discretionary Access Control (DAC) model with Mandatory Access Control (MAC) capabilities.
- AppArmor uses profiles to specify what resources a program may access.
- These profiles define the permissions granted to specific applications, restricting their access to files, directories, and network resources
**TCP Wrappers** 
- TCP Wrappers is a security feature for Unix-like operating systems that controls access to network services based on the IP address of the client attempting to connect.

#### Setting up

highly recommend dedicating time to learning about configuring important security measures such as `SELinux`, `AppArmor`, and `TCP wrappers` on your own.

**SELinux** 

| 1.  | Install SELinux on your VM.                                                                                  |
| --- | ------------------------------------------------------------------------------------------------------------ |
| 2.  | Configure SELinux to prevent a user from accessing a specific file.                                          |
| 3.  | Configure SELinux to allow a single user to access a specific network service but deny access to all others. |
| 4.  | Configure SELinux to deny access to a specific user or group for a specific network service.                 |

**AppArmor** 

| 5.  | Configure AppArmor to prevent a user from accessing a specific file.                                          |
| --- | ------------------------------------------------------------------------------------------------------------- |
| 6.  | Configure AppArmor to allow a single user to access a specific network service but deny access to all others. |
| 7.  | Configure AppArmor to deny access to a specific user or group for a specific network service.                 |

**TCP Wrappers** 

| 8.  | Configure TCP wrappers to allow access to a specific network service from a specific IP address.   |
| --- | -------------------------------------------------------------------------------------------------- |
| 9.  | Configure TCP wrappers to deny access to a specific network service from a specific IP address.    |
| 10. | Configure TCP wrappers to allow access to a specific network service from a range of IP addresses. |

***

## Remote Desktop Protocols in Linux

- Desktop protocol used in windows , linux and macos to provide graphical remote access to a system.
-  In addition, they can log in using different protocols if they want to install an application on their remote system. The most common protocols for this usage are RDP (Windows) and VNC (Linux).

**XServer** 
***

