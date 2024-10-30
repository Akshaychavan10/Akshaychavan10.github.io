---
title: 4 What is Networking
date: 2023-10-13 11:11:11 +/-TTTT
categories: [Notes, PreSecurity]
tags: [ipv4, subnet mask, internet] 
author: 1  
description: Networking - Connecting devices for communication and data exchange.  
---

## What is Networking

Networks are ubiquitous in various aspects of life, functioning as connections between entities with shared interests, functions, or purposes. In computing, networking involves the interconnection of technological devices, enabling communication and data exchange. Here are some key points to consider:

- **Definition of Networks:**
    - Networks are connections between entities, akin to friendship circles or infrastructure systems like transportation or power grids.
    - In computing, networks facilitate communication among devices, ranging from personal gadgets like phones and laptops to broader infrastructure like security cameras and traffic lights.
- **Scope of Networks:**
    - Networks can range from small-scale connections between two devices to extensive systems involving billions of interconnected devices.
    - They play integral roles in various domains, including gathering weather data, delivering electricity, and regulating traffic flow.
- **Importance in Cybersecurity:**
    - Networking is fundamental to cybersecurity due to its pervasive presence in modern-day life.
    - Understanding networking concepts is essential for safeguarding digital systems and data from potential threats and vulnerabilities.

## What is Internet.

- The Internet functions as a massive network comprising numerous smaller networks interconnected within it. Analogously, individuals like Alice can bridge communication between separate groups, forming a new network.
- The ARPANET project initiated the Internet's inception in the late 1960s, funded by the US Defense Department, marking the first operational network. However, it wasn't until Tim Berners-Lee invented the World Wide Web in 1989 that the Internet evolved into its modern form, facilitating information storage and sharing.
- Drawing parallels between Alice's friend network and computing devices, the Internet mirrors a larger version of this arrangement.
- The Internet is constituted by
    - private networks and
    - public networks connecting them.
    - Private networks are smaller networks, while public networks, collectively known as the Internet, interlink these smaller entities.
- Devices employ a set of labels to identify themselves within a network.

## Identify Device on Internet.

devices have two primary identification methods: IP addresses for routing data and MAC addresses akin to serial numbers. These identifiers are essential for maintaining network integrity and security.

## IPv4 Addresses

### IPv4 Structure

The most common method of assigning IP addresses is `IPv4`, which consists of a `32`-bit binary number combined into `4 bytes` consisting of `8`-bit groups (`octets`) ranging from `0-255`.

Thus an IPv4 address can look like this:

| **Notation** | **Presentation** |
| --- | --- |
| Binary | 0111 1111.0000 0000.0000 0000.0000 0001 |
| Decimal | 127.0.0.1 |

The IP network blocks were divided into `classes A - E`. The different classes differed in the host and network shares' respective lengths.

| **`Class`** | **Network Address** | **First Address** | **Last Address** | **Subnetmask** | **CIDR** | **Subnets** | **IPs** |
| --- | --- | --- | --- | --- | --- | --- | --- |
| `A` | 1.0.0.0 | 1.0.0.1 | 127.255.255.255 | 255.0.0.0 | /8 | 127 | 16,777,214 + 2 |
| `B` | 128.0.0.0 | 128.0.0.1 | 191.255.255.255 | 255.255.0.0 | /16 | 16,384 | 65,534 + 2 |
| `C` | 192.0.0.0 | 192.0.0.1 | 223.255.255.255 | 255.255.255.0 | /24 | 2,097,152 | 254 + 2 |
| `D` | 224.0.0.0 | 224.0.0.1 | 239.255.255.255 | Multicast | Multicast | Multicast | Multicast |
| `E` | 240.0.0.0 | 240.0.0.1 | 255.255.255.255 | reserved | reserved | reserved | reserved |

**subnet mask** :

- The IP address is divided into a `host part` and a `network part`.
    - Host Part : which identifies the specific device on that network, assigned by router at home or by an administrator.
    - Network Part : identifies the network the device is on, respective network administrator assigns the network part.
- 32-bit number used in conjection with ip address to identify the `network part` and `host part` of the address.
- For example, the subnet mask "255.255.255.0" (often expressed in shorthand notation as "/24") has the first 24 bits set to 1, indicating that the first 24 bits of the IP address represent the network portion, and the remaining 8 bits represent the host portion.
- When an IP address and subnet mask are bitwise ANDed together, the result determines the network address.
    - For example, consider the IP address 192.168.1.100 with a subnet mask of 255.255.255.0. In binary, the subnet mask would be 11111111.11111111.11111111.00000000. When bitwise ANDed with the IP address (11000000.10101000.00000001.01100100), the result (11000000.10101000.00000001.00000000) gives us the network address (192.168.1.0).

## Ping

Ping is a fundamental network tool that utilizes ICMP packets to assess the performance of connections between devices. By sending out ICMP echo packets and measuring the time it takes for them to travel to a target device and receive an echo reply, ping determines if a connection exists and if it's reliable.

This tool is commonly used to gauge connectivity within local networks, like your home network, or to test accessibility to external resources such as websites. Ping is readily available on operating systems like Linux and Windows, making it easily accessible. To perform a basic ping, you simply need to enter the command "ping" followed by the IP address or website URL you want to test.

`Ping 10.10.10.10`