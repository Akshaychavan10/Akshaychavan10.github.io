---
title: 7 Packets and Frames
date: 2023-10-10 11:11:11 +/-TTTT
categories: [Notes, PreSecurity]
tags: [tcpip, udpip] 
author: 1  
description: in-depth look at data transmission across networks, explaining the roles of packets, frames, and the OSI and TCP/IP models.   
---

## Introduction

- Packets and frames are small pieces of data that are assembled to become a file for transmission
- Packets and frames differ as frames only exist within the Data Link layer (layer 2) and packets are sent through the Network layer (layer 3) protocols which contains a frame at layer 2
- Packets are parts of a larger file or communication and are broken down for lower possibility of bottle-necking devices
- Packets are reassembled to make the complete file upon receipt

**Headers in IP (Internet Protocols)**

- **Time To Live (TTL)**: A header that is set to a value on the initialization of a communication, for every device which the packet travels across, the TTL value is decremented, this means that packets that don’t reach their end-point are eventually disposed of by a network device.
- Checksum: This header enables the input of a cryptographic hash to be appended to the **transmission**. If the checksum appended initially to the communication (upon sending) does not match the checksum which is present in the received message (upon receipt) then corruption or alteration has occurred during the transmission and the information is not valid.
- **Source Address**: Contains the origination IP of the communication, this enables for information to be returned to the source by the receiving device.
- **Destination Address**: The IP address which the outgoing communication is being sent to, this enables the packet to continue along a path to its destination.

**Encapsulation**
- Utilized by the OSI model, where the internal structure of frames only exist within layer 2 (Data Link) and packets are sent through the layer 3 (Network) protocols which contains a frame at layer 2

**File transmission across a network**
- A file would be broken into parts (x,y,z), which would be sent individually (x, then y, then z) and then reassembled to make the complete file (which contains x,y,z)

## TCP/IP 3-Way-Handshake.

### TCP/IP

- It is a referential model and literal protocol that aligns closely with real-world networking operations
- It only contains 4 layers, compared to the OSI model which has 7

**TCP layers**
- Application
- Transport
- Internet
- Network Interface

**Encapsulation and Decapsulation**

- Encapsulation occurs when information travels from the top layer (Application) to the lowest layer (Network Interface)
- Decapsulation occurs when information moves from the lowest layer (Network Interface) to the top layer (Application)

**TCP as a connection-based model**

- A session must be created between a server (host/sender) and client (receiver/requester) before information is put into transit

### Headers

- **Source Port** — Port opened by the sender which the TCP packet is sent from. This value is chosen from port number 0–65535 (inclusive) of which the port chosen cannot currently be in use.
- **Destination Port** — Port that an application or service is running on the remote host (device being requested from). This port is **not chosen randomly**
- **Source IP** — See *Source Address* in previous section.
- **Destination IP** — See *Destination IP* in previous section.
- **Sequence Number** — Once a connection is made between devices a random number is assigned to the header, this becomes the first packet sent by the transmission. Any additional packets sent will increment this number
- **Acknowledgement Number** — After sequence number is assigned, this value increments the sequence number by one for the next piece of data.
- **Checksum** — See *Checksum* in section above
- **Data** — The information which is being transmitted in the communication
- **Flag** — Determines how the packet should be handled by either device and is used to determine behaviour on both sides of the handshake (SYN-SYNACK-ACK).

### Flags

1. **SYN** — Initial flag sent by client on initialization of connection. Synchronizes the two devices.
2. **SYN/ACK** — sent by the receiving (server) device & acknowledges the attempt to synchronize by the client.
3. **ACK** — Acknowledgement that can be used the the client or server in order to acknowledge the receipt of a series of packets.
4. **DATA** — Once connection is established, data is sent via this flag.
5. **FIN** — used to *cleanly* close a connection between a client and server when work is complete.
6. **RST** — Abruptly end communication (an interrupt signal), is used as a last resort and signifies an issue such as inappropriate resource expenditure or a service/application error.

### test

## UDP/IP

### User Datagram Protocol (UDP)

- UDP is a network communication protocol that exists at the same layer as TCP (Transmission Control Protocol).
- It is a “stateless” protocol, meaning it does not track conversations or synchronize communication.
- While TCP operates like a structured conversation with defined turns and endpoints, UDP is like one person abruptly “screaming” information at another and then leaving afterwards.

**Characteristics:**

* Less overhead than TCP, due to the lack of connection management.
* Suitable for applications where data integrity is not critical,
* such as:
* Video streaming (where minor data loss does not significantly impact user experience)
* Online gaming (where quick response times are more important than perfect data transmission)