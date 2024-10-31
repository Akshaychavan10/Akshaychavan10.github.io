---
title: 1 Windows Primer
date: 2022-08-13 11:11:11 +/-TTTT
categories: [Notes, Windows]
tags: [introduction to windows] 
author: 1  
description: In depth guide to Windows Operating Systems.   
---

# Introduction

Personal version of windows

![version](https://ld-images-2.s3.us-east-2.amazonaws.com/Windows+Fundamentals/images/win-table.png)

Server Version of windows

![server versions](https://ld-images-2.s3.us-east-2.amazonaws.com/Windows+Fundamentals/images/win-table2.png)


# File System

the file system refers to the way files and directories are organised and stored on a storage device, such as a hard drive or SSD.

**File System Used by windows** :

*FAT* :

- File allocation table.
- Although the purpose of its emergence was to be used on floppy disks, it was adapted to be used in hard drives and other devices over time.
- Today its used in USB flash drives , flash drives and other embedded systems.

*exFAT* :

- Extensible File Allocation Table.
- optimized for flash memory such as USB flash drives and SD crads.

*NTFS* : 

- New Technology File System 
- Used as default file system in windows.
- Replace the FAT system as its more advanced and suitable for new technolgies.
- Also support Linux and BSD systems.

# Directory Structure

As in every operating system, the folder and file structure in Windows takes place in a certain order from the first installation.

![fundamental](https://ld-images-2.s3.us-east-2.amazonaws.com/Windows+Fundamentals/images/dir1.png)

1. PerfLogs : Store windows performace logs. (its found as an empty folder because the logging option is turned off by default.)
2. ProgramData : It stores Application Data that is not specific to any particular user. . Hidden folder.
3. Program Files : All the programs installed in the system located here.(64 bit)
4. Program Files(x86) : Its default installation location for 32 bit programs on a 64 bit windows system.
5. Users : Personal folder of each user who has logged on the system at least once.
6. Windows : the Windows Folder is where the entire operating system is installed.

# Windows Command Line

The command line is an intermediary program that receives the command from the user via the keyboard and transfer them to the OS for execution.


| Command                                | Description                                                                                                                                                                                                               |
| -------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `help dir`                             | provides detailed information about the cmd used on cmd line.                                                                                                                                                             |
| `dir`                                  | list files and folders                                                                                                                                                                                                    |
| `cd`                                   | change directory.                                                                                                                                                                                                         |
| `date`                                 | view and change the system's date information                                                                                                                                                                             |
| `echo`                                 | print on the screen.                                                                                                                                                                                                      |
| `hostname`                             | print hostname                                                                                                                                                                                                            |
| `time`                                 | see the system time                                                                                                                                                                                                       |
| `ipconfig`                             | Display information about network interface.                                                                                                                                                                              |
| `netstat -ano`<br>`-a`<br>`-n`<br>`-o` | Current network connections and their statuses.<br>Display all connections and listening ports<br>Display addresses and port numbers in numerical form.<br>Display the owning process ID associated with each connection. |
| `nslookup google.com`                  | Query DNS server to obtain domain name or IP address mapping.                                                                                                                                                             |
| `systeminfo`                           | detailed information about the system.                                                                                                                                                                                    |
| `type`                                 | print file content on screen.                                                                                                                                                                                             |
| `copy`                                 | used in file copy operation.                                                                                                                                                                                              |
| `rename`                               | rename the file.                                                                                                                                                                                                          |
| `mkdir`                                | create a new directory.                                                                                                                                                                                                   |
| `rename`                               | rename files.                                                                                                                                                                                                             |
| `move`                                 | move files.                                                                                                                                                                                                               |
| `tree`                                 | listing nested directories.                                                                                                                                                                                               |
| `rmdir`                                | remove directory                                                                                                                                                                                                          |

# Users and Groups in Windows

In windows users and groups are fundamental concepts used to manage access control and permission on the sytstem.

**Users**

- Users are individual accounts that represent people or services accessing the system. 
- Each user account has a unique username and a corresponding security identifier (SID). 
- Users can be categorized into different types, such as:
	- *Local Users* : User accounts that are created and managed on a specific Windows system.
	- *Domain Users*: User accounts that belong to a Windows domain and are managed by a domain controller.
	- *Built-in Users* : System-created user accounts with predefined roles, such as Administrator, Guest, and DefaultAccount.

**Groups** 

- Groups are collections of user accounts that share common access permissions.
- Windows includes several types of groups, including:
	- *Local Groups*: Groups that are created and managed on a specific Windows system.
	- *Domain Groups*: Groups that belong to a Windows domain and are managed by a domain controller.
	- *Built-in Groups* : Predefined groups with specific roles and permissions, such as Administrators, Users, Guests, and Backup Operators.

**Manage groups and users via cmd line**

| Command        | Description                                                                                                                       |
| -------------- | --------------------------------------------------------------------------------------------------------------------------------- |
| net user.      | display the usernames withing the system.                                                                                         |
| net accounts   | allow user to see the configuration related to password usage and login restrictions on the system.                               |
| net localgroup | displays the list of groups on the system.<br> If it is used with parameters, different operations can be executed on groups.<br> |
**Manage Groups and Users via GUI**
To manage Users and Groups via GUI open `Run` application by `Windows+R` and type `lusrmgr.msc` .

![user8](https://ld-images-2.s3.us-east-2.amazonaws.com/Windows+Fundamentals/images/user8.png)

we can create users and group in `Actions` tab.

# Permission Management on Windows

**Permissions** 

- Permissions define the actions that users or groups are allowed or denied to perform on files, folders, registry keys, and other system resources.

**Access Control Lists(ACLs)**

- ACLs are data structure associated with objects (such as files and folders) that defines permission granted to users and groups.

| Permissions                                                                                                                                                                                                                                         | Access control Lists                                                                                    |
| --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------- |
| Think of permissions as rules or settings that determine what actions someone can take with a file, folder, or any other resource on a computer. These actions could include things like reading, writing, deleting, or executing (running) a file. | An ACL is like a list attached to that file that holds all the permissions for who can do what with it. |


**Permissions Types** 

Windows have six permission types when managing file permissions 

1. Full control
2. Modify
3. Read & Execute
4. Read
5. Write
6. special permission.

![permissions](https://ld-images-2.s3.us-east-2.amazonaws.com/Windows+Fundamentals/images/perm2.png)

**Viewing File Permissions** 

Right-Click on file and open `properties` .

![proper](https://ld-images-2.s3.us-east-2.amazonaws.com/Windows+Fundamentals/images/perm1.png)

**Changing file permissions** 

right-click on the file and open the "properties" window, then click the "advanced" button in the "security" tab.

![perm3](https://ld-images-2.s3.us-east-2.amazonaws.com/Windows+Fundamentals/images/perm3.png)

We can change permission in `security` tab.

![permt5](https://ld-images-2.s3.us-east-2.amazonaws.com/Windows+Fundamentals/images/perm5.png)

**User Account Control** 

- User Account Control (UAC) is a security feature in Windows that helps prevent unauthorized changes to your computer.
- The primary purpose of UAC is to mitigate the impact of malicious software or unintentional changes made by users that could potentially harm the system. 
- It achieves this by prompting users for confirmation or consent before allowing certain actions to be executed, particularly those that require administrative privileges. These actions include:

	1. Installing or uninstalling software.
	2. Making changes to system settings.
	3. Installing device drivers.
	4. Running certain applications with elevated privileges.

- UAC has 4 level.

	1. *Always Notify* : you are notified before applications and users make changes that require administrative permissions.
	2. *Notify me only when apps try to make changes to my computer (default)* : UAC notifies you only before programs make changes that require administrative permissions. malicious program can be created to simulate the keystrokes or mouse movements made by a user and change windows settings.
	3. *Notify me only when apps try to make changes to my computer (do not dim my desktop)* : This level is identical to the previous setting except for the fact that, when a UAC prompt is shown, the desktop is not dimmed and other desktop apps can interfere with it. This level is even less secure as it makes it even easier for malicious programs to simulate keystrokes or mouse moves that interfere with the UAC prompt.
	4. *Never notify me* : At this level, UAC is turned off, and it doesn't offer any protection against unauthorized system changes.

# Windows Process Management

**Process** : A process is software program that is currently running in windows. Each process has its own `PID` and they are logged in each process operation.

**Process Tree** : Running a program is a process. From this process, another process can be created. There is a parent-child relationship between the two processes.

**Parent Process** : In computing, a parent process is a process that has created one or more child processes.

**Child Process** : A child process in computing is a process created by another process (the parent process). A parent process may have multiple child processes, but a child process only one parent process.

> `Process Hacker` is tool to view the process tree.

![proc2](https://ld-images-2.s3.us-east-2.amazonaws.com/Windows+Fundamentals/images/proc2.png)

### Windows Legitimate Processes

**wininit.exe** 

- know as `Windows Intilization Process`.
- Responsible for starting 
	- `services.exe` (service control manager)  
	- `lsass.exe` (Local security authority subsystem service)
	- `lsm.exe` (Local session manager.)
- This process created during system boot and works with the privileges of the most authorized user (NT AUTHORITY\SYSTSEM) on the system.

**Services.exe** :

- responsible for starting and stopping services.
- Child processes :
	- `Svchost.exe` 
	- `dllhost.exe`
	- `taskhost.exe`
	- `spoolsv.exe`.
- works with the privileges of the most authorized user (NT AUTHORITY\SYSTEM) on the system.
- There should only be 1 "services.exe" process at a time in the process tree under normal conditions. If there are multiple "services.exe" processes or if there is a process with a similar name, it should be investigated further as it may be a process that belongs to a malicious activity.

**svchost.exe** :

- It is a shared service process that allows many windows services to share single process.
- Sharing a single process help windows to decrease its overall resource usage.
- This is the process running on the system with NT AUTHORITY\NETWORK SERVICE or NT AUTHORITY\SYSTEM privileges.

**lsass.exe** :

- process responsible for critical security operations such as confirming or rejecting users' passwords during login in the Windows operating system
-  In addition, this process works actively during the password changes of users.
- mimikatz tool used to obtain password from lsass.exe process.

**winlogon.exe** :

- Performs the login and logout operations of the users.
- `lsass.exe` focuses on security related tasks, paticularly authentication and enforcing security policies, while `winlogon.exe` manages the user login and logout processes.

**Explorer.exe** :

- Its a parent process of almost every process that has a GUI in windows operating system and opens as a window.

**Task Manager** :

- Application that enables to see and manage the processes on windows operating system.

**Commands Related to Process Operations** :


| `cmd`      | Description                                                                 |
| ---------- | --------------------------------------------------------------------------- |
| `tasklist` | allow us to see the list of processes running on the system.                |
| `taskkill` | used to terminate the processes running on the system. `taskkill /PID 2812` |



# Windows Services

- applications that run in the background on Microsoft Windows operating systems, often without any direct user interface.
- These services handle various system-level tasks such as managing network connections, printing, file sharing, system security, and more.

**Managing Windows Services with a Graphical User Interface**

- Open `Run` application with `Windows+R` and type `service.msc`.
![](https://ld-images-2.s3.us-east-2.amazonaws.com/Windows+Fundamentals/images/srv2.png)

**Managing windows service with command line** 

| `cmd`                             | Description                                   |
| --------------------------------- | --------------------------------------------- |
| `sc query`                        | Display all running services                  |
| `sc query type-service state-all` | Display all services available in the system. |
| `sc query <service_name>`         | Get info about the service.                   |
| `sc start <service_name>`         | Start and Stop service.                       |

# Task Scheduler 

- Task Scheduler is execution of certain operation on the system at certain time intervals or at certain times.

**Managing Scheduled Tasks with GUI**

1. Open `Run` application with `Windows+R` and type `taskschd.msc`

![task2](https://ld-images-2.s3.us-east-2.amazonaws.com/Windows+Fundamentals/images/task2.png)

2. click on `Task Scheduler Library` to view and create new tasks.

**Managing Scheduled Tasks with Command Line** 

| `cmd`                                     | Description                  |
| ----------------------------------------- | ---------------------------- |
| `schtasks /query`                         | Display all scheduled tasks. |
| `schtasks /create /TN "task_name`         | Create new Task              |
| `schtasks /delete /TN "task_name`         | Delete task                  |
| `schtasks /query /TN "task_name`          | Get info about specific task |
| `schtasks /run /TN "task_name`            | Run Task                     |
| `schtasks /change /TN "task_name /ENABLE` | Enable or Disable task.      |

# Windows Registry

- Windows Registry is a hierarchical database that contains the operating system and system configuration related to the program installed in the system.
- It keeps the info and settings of programs and hardware in this database. eg : when a program is installed on windows, the program may choose to keep the expiry date of its license in the windows Registry.
- The registry contains important configuration and information about the system, also info about other programs installed on the system.
- you can access rigistry by opening `Run` application and open `regedit` program.

![reg2](https://ld-images-2.s3.us-east-2.amazonaws.com/Windows+Fundamentals/images/reg2.png)

**Registry Structure** 

- windows registry entries are located at `%SystemRoot%\System32\Config` .
- Registry contains two essential elements. `keys`(container objects similar to folders) and `values` (values are non-container objects similar to files).

There are seven predefined root keys :  
  
- HKEY_LOCAL_MACHINE or HKLM
- HKEY_CURRENT_CONFIG or HKCC
- HKEY_CLASSES_ROOT or HKCR
- HKEY_CURRENT_USER or HKCU
- HKEY_USERS or HKU
- HKEY_PERFORMANCE_DATA (only in Windows NT, but invisible in the Windows Registry Editor)
- HKEY_DYN_DATA (only in Windows 9x, and visible in the Windows Registry Editor)

**HKEY_LOCAL_MACHINE or HKLM**

- Section where computer-specific hardware and software configuration information kept.
- Settings applicable to each logged-in user are maintained in this section.

![reg3](https://ld-images-2.s3.us-east-2.amazonaws.com/Windows+Fundamentals/images/reg3.png)

1. Hardware : contains information about the hardware devices connected to the system.
2. SAM : This section contains the encrypted versions of the passwords of the users.
3. SECURITY : It is the section that contains the security policies in the system.
4. SOFTWARE :  This section contains the configurations of the operating system services as well as the programs installed in the system.
5. SYSTEM : It is the section where the configuration information of the system is kept.

**HKEY_CURRENT_CONFIG or HKCC** 

- It is the section that keeps the hardware configurations during the operation of the system.

**HKEY_CLASSES_ROOT or HKCR**

- It contains software settings, shortcuts, and all other user interface-related information. If this partition is deleted, none of the files will open, even if Windows is running.  
  
**HKEY_CURRENT_USER or HKCU**

- This is the section where the configurations of the logged-in user are kept.  
  
**HKEY_USERS or HKU**

- It is the section where the configurations of all users registered to the system are kept.

**Registry Operations on the Command Line** :

Many operations that can be executed with the registry editor program can also be executed via the command line. Reading registry keys and values, writing values to the registry, and exporting and importing keys are some of the operations that can be executed via the command line.



# Windows Firewall

Windows Firewall is a security tool that allows blocking or allowing incoming network packets to the host machine and outgoing network packets from the host machine within the framework of certain rules.

- Firewall Rule : It contains set of conditions used to determine whether a network packet is allowed to pass through the firewall.
- Inbount Rule : An inbound rule filters traffic that passes from a network to a local computer based on the rules’ parameters.
- Outbound Rules : traffic sent from local computers to the network should be filtered according to the filtering rules.

### Firewall Rule Management with GUI

- Open `Run` application with `Windows+R` and type `wf.msc`.

![fw2](https://ld-images-2.s3.us-east-2.amazonaws.com/Windows+Fundamentals/images/fw2.png)

### Firewall Rule Management with Command Line


| `cmd`                                                     | Description                                 |
| --------------------------------------------------------- | ------------------------------------------- |
| `netsh advfirewall firewall show rule name=all`           | List all firewall Rules.                    |
| `netsh advfirewall firewall show rule name="rule_name"`   | Display info of the specific firewall rule. |
| `netsh advfirewall firewall delete rule name="rule_name"` | Delete firewall rule.                       |

# Event Logs

Event Logs are logs collected through the Windows operating system.
- Powershell activities
- Deleting event logs
- Starting and stopping services
- Creating a new scheduled task
- RDP activity
- Changing user privileges
- Failed login activities

**Event log structure** 

- Each event in log entry has an Event ID. 
- In windows system there are three main event log titles as "Applications", "System" and "Security".

![evt1](https://ld-images-2.s3.us-east-2.amazonaws.com/Windows+Fundamentals/images/evt1.png)

1. Application : Stores log related to the applications in the system. eg : you can find errors received by an antivirus application running on the system.
2. System : It is the area where the logs created by the basic components of the operating system are located. For example, logs for a driver loads and unloads operations can be found here.
3. Security : Records regarding authentication and security are kept here.

### Viewing Event Logs with Event Viewer

- Open `Run` application and write `eventvwr`
![](https://ld-images-2.s3.us-east-2.amazonaws.com/Windows+Fundamentals/images/evt3.png)

- Lets Examine the logs of failed logins
![](https://ld-images-2.s3.us-east-2.amazonaws.com/Windows+Fundamentals/images/evt5.png)

- Filtering Event logs
![](https://ld-images-2.s3.us-east-2.amazonaws.com/Windows+Fundamentals/images/evt6.png)

### Viewing Event logs via Command Line

`wevtutil query-events Security /rd:true /count:1 /format:text /q:"Event[System[(EventID=4625)]]"` 

```
"query-events" parameter : Query events from a log or log file.
"/rd" parameter : Reverse direction.
"/count" parameter : Log count.
"/format" parameter : Output format.
"/q" parameter : XPathQuery
```


# Windows Management Instrumentation (wmi)

- Its feature used to access components of the windows operating system.
- WMI allows local and remote access.

**Using WMI**

| `cmd`                       | Description                       |
| --------------------------- | --------------------------------- |
| `wmic os list brief`        | info about operating system.      |
| `wmic useraccount get name` | list names of user in the system. |

> Find Detailed info here : https://ss64.com/nt/wmic.html

