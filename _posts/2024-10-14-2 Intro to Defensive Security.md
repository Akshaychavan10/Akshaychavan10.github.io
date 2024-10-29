---
title: 2 Intro to Defensive Security
date: 2023-10-14 11:11:11 +/-TTTT
categories: [Notes, PreSecurity]
tags: [blueteam] 
author: 1  
description: A comprehensive guide on Defensive Security to protecting systems from threats, including prevention, detection, response, and recovery.   
---

## Intro to Defensive Security:
Before delving into defensive security, let's understand its counterpart, offensive security, which involves breaching systems. Offensive tactics include exploiting defects, unsafe installations, and access control policies. Red teams and penetration testers typically handle offensive security.

**Defensive Security Overview:**

- Preventing intrusions from occurring.
- Detecting and responding to intrusions promptly.

**Tasks Associated with Defensive Security:**

- **User Cyber Security Awareness:** Educating users to secure systems against various attacks.
- **Documenting and Managing Assets:** Understanding and managing systems and gadgets effectively.
- **Updating and Patching Systems:** Ensuring all devices are up-to-date and patched against vulnerabilities.
- **Setting up Preventative Security Devices:** Implementing Intrusion Prevention Systems (IPS) and firewalls to limit network traffic and block malicious activities.
- **Setting up Logging and Monitoring Devices:** Employing effective network logging and monitoring to detect malicious actions and unauthorized devices.



## Areas of Defensive Security 

### Security Operations Center (SOC):

A team of cybersecurity experts monitors networks and systems to detect malicious events effectively. Key areas of interest for a SOC include:

1. **Vulnerabilities:**
    
    - Promptly address system weaknesses by applying patches or updates.
    - Take appropriate precautions to prevent exploitation when a remedy isn't available.
2. **Policy Violations:**
    
    - Enforce adherence to security policies to protect networks and systems.
    - Identify and address policy violations, such as unauthorized data transfers.
3. **Unauthorized Activity:**
    
    - Recognize and block unauthorized access attempts promptly.
    - Respond to incidents where user credentials are compromised to prevent further damage.
4. **Network Intrusion:**
    
    - Detect and respond to intrusions promptly, regardless of existing security measures.
    - Monitor for malicious links and abusive server activities to prevent infiltration.

### Threat Intelligence:

Threat intelligence is achieved through "Threat-Informed Defense," aiming to acquire actual or possible intelligence to better prepare against future adversaries. Despite each attacker having different objectives, gathering extensive information is crucial to prevent incidents.

**Data Collection for Intelligence:**

- Collect, process, and analyze data to acquire actionable intelligence.
- Gather data from both local and public sources, including network logs and forums.
- Organize data into a format suitable for analysis.

**Analysis Phase Goals:**

- Understand attackers' motivations and behaviors.
- Compile a list of recommendations and practical activities based on the analysis.

### Digital Forensics and Incident Response (DFIR) : 
There are 3 components to this:
1. Digital Forensics
2. Incident Response
3. Malware Analysis
#### Digital Forensics:

Digital forensics app
lies scientific methods to investigate crimes and establish facts, particularly in the realm of digital devices like computers and smartphones. Initially emerging as computer forensics, it evolved into digital forensics to address associated crimes effectively.

**Focus Areas of Digital Forensics:**

1. **File System Analysis:**
    
    - Analyze digital forensics images to extract information from a system's storage, including installed programs, produced files, partially overwritten data, and deleted files.
2. **System Memory Examination:**
    
    - Create forensic images of system memory to evaluate malicious program contents and understand the nature of an attack, especially if the attacker operates solely in memory.
3. **System Logs Investigation:**
    
    - Explore different log files on client and server machines to uncover information about system activities. Even if attackers attempt to erase traces, remnants often remain in log files.
4. **Network Logs Review:**
    
    - Examine logs of network packets traversing a network to ascertain whether an attack occurred and understand its characteristics.

#### Incident Response
In cybersecurity, an incident refers to various events, from data breaches to policy violations. Incident response aims to minimize harm and recover swiftly from such occurrences. It encompasses several phases:

1. **Preparation:**
    
    - Establish a trained team ready to respond to incidents promptly.
    - Implement preventive measures to reduce the likelihood of incidents occurring.
2. **Detection and Analysis:**
    
    - Utilize resources to detect and investigate incidents thoroughly, assessing their severity.
3. **Containment, Eradication, and Recovery:**
    
    - Upon identification, swiftly contain the incident to prevent further damage.
    - Eradicate the threat and restore affected systems to their normal functioning.
    - For instance, if a system is infected with a virus, efforts focus on stopping its spread, cleaning infected systems, and ensuring recovery.
4. **Post-Incident Activity:**
    
    - After successful recovery, compile a detailed report.
    - Communicate lessons learned to prevent similar incidents in the future.
#### Malware Analysis

Malware, short for malicious software, encompasses various harmful programs, documents, and data. Understanding different types of malware is crucial for effective cybersecurity. Here are some examples:

1. **Virus:**
    
    - Code that attaches to other programs, spreading from one machine to another.
    - Modifies, overwrites, or deletes files, potentially rendering the computer slow or unusable.
2. **Trojan Horse:**
    
    - Presents itself as benign software while concealing malicious functions.
    - Victims may unknowingly download it, granting attackers unauthorized access to their systems.
3. **Ransomware:**
    
    - Encrypts user files, making them inaccessible without a decryption key.
    - Attackers demand a ransom in exchange for the decryption key, holding data hostage.

Malware analysis involves studying these malicious programs using various methods:

- **Static Analysis:**
    
    - Examining the malware without executing it.
    - Requires knowledge of assembly language for a thorough understanding.
- **Dynamic Analysis:**
    
    - Observing the malware's behavior in a controlled environment.
    - Allows insight into how the malware operates when active.





