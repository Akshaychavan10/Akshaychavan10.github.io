---
title: 4 System Management
date: 2022-08-24 11:11:11 +/-TTTT
categories: [Notes, Linux]
tags: [user management, packages, service, process] 
author: 1  
description: detailed guide on various functionalities and tools available in Linux systems, encompassing user management, package management, services and processes, task scheduling, network services, and more. 
---


## User Management

Here is a list that will help us to better understand and deal with user management.

| **Command** | **Description**                                                                                                                                             |
|-------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------|
| `sudo`      | Execute command as a different user.                                                                                                                        |
| `su`        | The `su` utility requests appropriate user credentials via PAM and switches to that user ID (the default user is the superuser). A shell is then executed. |
| `useradd`   | Creates a new user or update default new user information.                                                                                                  |
| `userdel`   | Deletes a user account and related files.                                                                                                                   |
| `usermod`   | Modifies a user account.                                                                                                                                    |
| `addgroup`  | Adds a group to the system.                                                                                                                                 |
| `delgroup`  | Removes a group from the system.                                                                                                                            |
| `passwd`    | Changes user password.                                                                                                                                      |


***
## Package Management
- Packages are archives that contain binaries of software, configuration files, information about dependencies and keep track of updates and upgrades.
- The features that most package management systems provide are:
	- Package downloading
	- Dependency resolution
	- A standard binary package format
	- Common installation and configuration locations
	- Additional system-related configuration and functionality
	- Quality control

- package management programs.

| **Command** | **Description** |
|-------------|-----------------|
| `dpkg`      | The `dpkg` is a tool to install, build, remove, and manage Debian packages. The primary and more user-friendly front-end for `dpkg` is aptitude. |
| `apt`       | Apt provides a high-level command-line interface for the package management system. |
| `aptitude`  | Aptitude is an alternative to apt and is a high-level interface to the package manager. |
| `snap`      | Install, configure, refresh, and remove snap packages. Snaps enable the secure distribution of the latest apps and utilities for the cloud, servers, desktops, and the internet of things. |
| `gem`       | Gem is the front-end to RubyGems, the standard package manager for Ruby. |
| `pip`       | Pip is a Python package installer recommended for installing Python packages that are not available in the Debian archive. It can work with version control repositories (currently only Git, Mercurial, and Bazaar repositories), logs output extensively, and prevents partial installs by downloading all requirements before starting installation. |
| `git`       | Git is a fast, scalable, distributed revision control system with an unusually rich command set that provides both high-level operations and full access to internals. |


#### Advanced package manager (APT)

- Debian-based Linux Distributions use the APT package manager.
- package is archive file containing multiple `.deb` files.
- the `dpkg` utility is used to install programs from associated `.deb` file.
-  Many program have dependency and apt make easy to install program with its dependency also.
- apt uses a database called apt cache. this is used to provide information about packages installed on our system offline.
	- `apt-chache search impacket` this will search to find all impacket related packages.

You can also install program downloaded from internet with `dpkg` cmd.

```
sudo dpkg -i program_name
```



***
***
## Service And Process Management

#### Systemctl

```
#start the service
systemctl start ssh

# check status of service.
systemctl status ssh 

# to add program to Sysv script to tell the system to run this service after startup
systemctl enable ssh

# list all services
systemctl list-units --type=service

# For some reason sometimes system do not start due to some error. to view logs and error
journalctl -U ssh.service --no-pager


```

#### Kill process

- states of process
	- Running
	- Waiting (waiting for an event or system resource)
	- Stopped
	- Zombie (stopped but still has an entry in the process table).
- Processes can be controlled using `kill`, `pkill`, `pgrep`, and `killall`.
- To interact with process, we must send a signal to it. Most commonly used signals are :

|**Signal**|**Description**|
|---|---|
|`1`|`SIGHUP` - This is sent to a process when the terminal that controls it is closed.|
|`2`|`SIGINT` - Sent when a user presses `[Ctrl] + C` in the controlling terminal to interrupt a process.|
|`3`|`SIGQUIT` - Sent when a user presses `[Ctrl] + D` to quit.|
|`9`|`SIGKILL` - Immediately kill a process with no clean-up operations.|
|`15`|`SIGTERM` - Program termination.|
|`19`|`SIGSTOP` - Stop the program. It cannot be handled anymore.|
|`20`|`SIGTSTP` - Sent when a user presses `[Ctrl] + Z` to request for a service to suspend. The user can handle it afterward.|


```
kill 9 <PID>
```

#### Background a Process
- Put the process in background and to continue using current session to interact with another process.
-  `[Ctrl + z]` used to suspend the process. they will not execute further. 
- To keep it running in the background we have to enter `bg` command.
- Another option is to put process background is to use `&` after the cmd.
- `jobs` to list all background processes.

- To foreground a process we use `fg` cmd. Example `fg <ID>`  here ID can be found using `jobs` cmd.

#### Execute Multiple Commands


**Semicolon (;)** : The semicolon (`;`) is a command separator. Ignore error and result of previous cmd. (result or errors of previous cmd doesnt effect the next cmd)
```shell-session
cyber7ron@htb[/htb]$ echo '1'; ls MISSING_FILE; echo '3'

1
ls: cannot access 'MISSING_FILE': No such file or directory
3
```

**- Double `ampersand` characters (`&&`)** :  run the commands one after the other. If there is an error in one of the commands, the following ones will not be executed anymore, and the whole process will be stopped.
```shell-session
cyber7ron@htb[/htb]$ echo '1' && ls MISSING_FILE && echo '3'

1
ls: cannot access 'MISSING_FILE': No such file or directory
```

**Pipes (`|`)** :  depend not only on the correct and error-free operation of the previous processes but also on the previous processes' results.



***
***
## Task Scheduling
- allow user to schedule and automate tasks. eg : updating software, running scripts, cleaning databases, and automating backups
- In addition, alerts can be set up to display when certain events occur or to contact administrators or users.
#### Systemd

- its service used in linux system to start processes and scripts at a specific time. to do so we need 
	1. Create a timer
	2. Create a service 
	3. Activate the timer

**1. Create a timer** :

1. create a directory where the timer script will be stored.

```shell-session
cyber7ron@htb[/htb]$ sudo mkdir /etc/systemd/system/mytimer.timer.d
cyber7ron@htb[/htb]$ sudo vim /etc/systemd/system/mytimer.timer
```
2. create a script that configure the timer. the script must contain `Unit`, `Timer`, and `Install` options.  
> Unit = description for the timer

> Timer = when to start the timer and when to activate it

> Install = where to install the time


==Mytimer.timer== 

```
[Unit]
Description=My Timer

[Timer]
OnBootSec=3min
OnUnitActiveSec=1hour

[Install]
WantedBy=timers.target
```

`OnBootSec` if we want to run our script only once after the system boot.
`OnUnitActiveSec` if we want our script to run regularly.

**2. Create a Service** : 

```shell-session
cyber7ron@htb[/htb]$ sudo vim /etc/systemd/system/mytimer.service
```

Here we set a description and specify the full path to the script we want to run. The "multi-user.target" is the unit system that is activated when starting a normal multi-user mode.

```
[Unit]
Description=My Service

[Service]
ExecStart=/full/path/to/my/script.sh

[Install]
WantedBy=multi-user.target
```

*Reload Systemd* :
Reload the systemd  to include changes.

```shell-session
cyber7ron@htb[/htb]$ sudo systemctl daemon-reload
```

**3. Start the Timer & Service**

```shell-session
cyber7ron@htb[/htb]$ sudo systemctl start mytimer.service
cyber7ron@htb[/htb]$ sudo systemctl enable mytimer.service
```



#### Cron
- allow user and administrators to execute tasks at a specific time or within specific intervals. we just need to create a script and tell the cron daemon to call it at a specific time.
- Process to set up cron daemon is different than `systemd` . to set up the cron daemon, we need to store the tasks in a file called `crontab` and then tell daemon when to run the tasks.

The structure of Cron consists of the following components

| **Time Frame**         | **Description**                                                       |
| ---------------------- | --------------------------------------------------------------------- |
| Minutes (0-59)         | This specifies in which minute the task should be executed.           |
| Hours (0-23)           | This specifies in which hour the task should be executed.             |
| Days of month (1-31)   | This specifies on which day of the month the task should be executed. |
| Months (1-12)          | This specifies in which month the task should be executed.            |
| Days of the week (0-7) | This specifies on which day of the week the task should be executed.  |

```
# Example of job definition: 
# .---------------- minute (0 - 59) 
# | .------------- hour (0 - 23) 
# | | .---------- day of month (1 - 31) 
# | | | .------- month (1 - 12) OR jan,feb,mar,apr ... 
# | | | | .---- day of week (0 - 6) (Sunday=0 or 7)  
# | | | | | 
# * * * * * user-name command to be executed

```

For example

```txt
# System Update
* */6 * * /path/to/update_software.sh

# Execute scripts
0 0 1 * * /path/to/scripts/run_scripts.sh

# Cleanup DB
0 0 * * 0 /path/to/scripts/clean_database.sh

# Backups
0 0 * * 7 /path/to/scripts/backup.sh
```

The "System Update" should be executed every sixth hour. This is indicated by the entry `*/6` in the hour column. The task is executed by the script `update_software.sh`, whose path is given in the last column.

The task `execute scripts` is to be executed every first day of the month at midnight. This is indicated by the entries `0` and `0` in the minute and hour columns and `1` in the days-of-the-month column. The task is executed by the `run_scripts.sh` script, whose path is given in the last column.

The third task, `Cleanup DB`, is to be executed every Sunday at midnight. This is specified by the entries `0` and `0` in the minute and hour columns and `0` in the days-of-the-week column. The task is executed by the `clean_database.sh` script, whose path is given in the last column.

The fourth task, `backups`, is to be executed every Sunday at midnight. This is indicated by the entries `0` and `0` in the minute and hour columns and `7` in the days-of-the-week column. The task is executed by the `backup.sh` script, whose path is given in the last column.

***
***
## Network Services

#### SSH
- Network protocol that allow the secure transmission of data and commands over a network.
- `openssh` is a most commonly used ssh server.
- openssh can be configured and customized by editing the file `/etc/ssh/sshd_config`. 

#### NFS
- Network File System is a network protocol that allow us to store and manage files on remote system as if they were stored on the local system.
- there are several NFS servers, including NFS-UTILS (`Ubuntu`), NFS-Ganesha (`Solaris`), and OpenNFS (`Redhat Linux`)
- We can configure NFS via the configuration file `/etc/exports`.
	- Here are some important access rights that can be configured in NFS:

| **Permissions**  | **Description**                                                                                                                                            |
| ---------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `rw`             | Gives users and systems read and write permissions to the shared directory.                                                                                |
| `ro`             | Gives users and systems read-only access to the shared directory.                                                                                          |
| `no_root_squash` | Prevents the root user on the client from being restricted to the rights of a normal user.                                                                 |
| `root_squash`    | Restricts the rights of the root user on the client to the rights of a normal user.                                                                        |
| `sync`           | Synchronizes the transfer of data to ensure that changes are only transferred after they have been saved on the file system.                               |
| `async`          | Transfers data asynchronously, which makes the transfer faster, but may cause inconsistencies in the file system if changes have not been fully committed. |

***
***
## Working with Web Services

#### Curl
- allow us to transfer file from the shell over protocols like `HTTP`,`HTTPS`,`FTP`, `SFTP` , `FTPS` , or `SCP`.
- we can also view individual requests to look at the client's and server's communication.

#### Wget
- An alternative to curl is wget.
- we can download files from FTP or HTTP servers directly from the terminal.

#### Python 3
- python also used to transfer data.

```python3

python3 -m http.server

```

***
***
## Backup and Restore

- on ubuntu system, we use following tools to backup our data.
	- Rsync
		- Rsync is an open-source tool that allows us to quickly and securely back up files and folders to a remote location.
		- It is particularly useful for transferring large amounts of data over the network because it only transmits the changed parts of a file.

	- Deja Dup
		- graphical backup tool for Ubuntu that simplifies the backup process, allowing us to quickly and easily back up our data.
		- It uses Rsync as a backend and also supports data encryption.
		-  It provides a user-friendly interface to create backup copies of data on local or remote storage media.

	- Duplicity
		- Duplicity is another graphical backup tool for Ubuntu that provides users with comprehensive data protection and secure backups.
		- uses Rsync as a backend and additionally offers the possibility to encrypt backup copies and store them on remote storage media, such as FTP servers, or cloud storage services, such as Amazon S3.

#### Install Rsync
```shell-session
cyber7ron@htb[/htb]$ sudo apt install rsync -y
```

#### Rsync - Backup a local Directory to our Backup-Server
```shell-session
cyber7ron@htb[/htb]$ rsync -av /path/to/mydirectory user@backup_server:/path/to/backup/directory
```

copy the entire directory `/path/to/mydirectory` to a remote host `backup_server` , to the directory `/path/to/backup/directory`.

 The option `archive` (`-a`) is used to preserve the original file attributes, such as permissions, timestamps, etc., and using the `verbose` (`-v`) option provides a detailed output of the progress of the `rsync` operation.

#### Rsync - Restore our Backup
```shell-session
cyber7ron@htb[/htb]$ rsync -av user@remote_host:/path/to/backup/directory /path/to/mydirectory
```

#### Encrypted Rsync
we can combine the use of SSH and other security measures. By using SSH, we are able to encrypt our data as it is being transferred
```shell-session
cyber7ron@htb[/htb]$ rsync -avz -e ssh /path/to/mydirectory user@backup_server:/path/to/backup/directory
```

#### Auto-synchronization
To enable auto-synchronization using `rsync`, you can use a combination of `cron` and `rsync` to automate the synchronization process.

**Rsync_backup.sh**

```bash
#!/bin/bash

rsync -avz -e ssh /path/to/mydirectory user@backup_server:/path/to/backup/directory
```

in order to ensure that the script is able to execute properly, we must provide the necessary permissions.

**Auto-Sync- Crontab**

```shell-session
0 * * * * /path/to/RSYNC_Backup.sh
```

crontab that tells `cron` to run the script every hour at the 0th minute.


***
***
## File System Management
-  process that involves organizing and maintaining the data stored on a disk or other storage device.
-  Linux supports a wide range of file systems, including ext2, ext3, ext4, XFS, Btrfs, NTFS, and more.

#### File system in linux
- The Linux file system is based on the Unix file system, which is a hierarchical structure that is composed of various components.
- At the top of this structure is the **inode table** 
	- The inode table is a table of information associated with each file and directory on a Linux system. 
	- Inodes contain metadata about the file or directory, such as its permissions, size, type, owner, and so on. 
	- The inode table is like a database of information about every file and directory on a Linux system.
- In Linux Files can be stored in two way
	- 1. Regular files
		- most common type of file, and they are stored in the root directory of the file system.
	- 2. Directories.
		- Directories are used to store collections of files.
		- When a file is stored in a directory, the directory is known as the parent directory for the files.
	-  3. Symbolic Links.
		- references to other files or directories. Symbolic links can be used to quickly access files that are located in different parts of the file system.

#### Disk & Drives
- Disk management on Linux involves managing physical storage devices, including hard drives, solid-state drives, and removable storage devices.
- `fdisk` tool used for disk management.
- It allow us to create, delete and manage partitions on a drive. also display info about partition table, including size and type of each partition.
- Other  partition tools are `gpart`, and `GParted`.

#### Mounting
- Each logical partition or drive needs to be assigned to a specific directory on Linux. This process is called mounting. 
- Mounting involves attaching a drive to a specific directory, making it accessible to the file system hierarchy.
- `mount` tool is used to mount file system on linux.  and `/etc/fstab` file used to define the default file system that are mounted at boot time.

```bash

# List mounted Drives 
mount

# to mount usb drive. mount a USB drive with the device name `/dev/sdb1` to the directory `/mnt/usb`
sudo mount /dev/sdb1 /mnt/usb

# unmount a file system in Linux. need proper permissions and files cannot be used by any running processes.
sudo umount /mnt/usb

# list open files on the file system. so we can close files that are used by any processes.
lsof | grep cyber7ron
```

#### Swap
- Important and crucial aspect of memory management.
- It ensure that system runs smoothly , even when the available physical memory is depleted.
- When the system runs out of physical memory, the kernel transfers inactive pages of memory to the swap space, freeing up physical memory for use by active processes. This process is known as swapping.

***
***

## Containerization
- Containerization is a process of packaging and running applications in isolated environment. eg : container, virtual machine or serverless environment.
- On linux Docker,Docker Compose , and linux container make this process possible.

#### Docker

- Docker is open-source platform for automating the deployment of applications as self-contained units called `containers`.

**Installing Docker-Engine**

- We can use following script to install docker it on ubuntu.

```
#!/bin/bash

# Preparation
sudo apt update -y

sudo apt install ca-certificates curl gnupg lsb-release -y

sudo mkdir -m 0755 -p /etc/apt/keyrings

curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /etc/apt/keyrings/docker.gpg

echo "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.gpg] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable" | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null

# Install Docker Engine

sudo apt update -y
sudo apt install docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin -y

# Add user htb-student to the Docker group

sudo usermod -aG docker htb-student
echo '[!] You need to log out and log back in for the group changes to take effect.'

# Test Docker installation

docker run hello-world
```

- Docker engine and specific docker images are needed to run a container. (these can be obtained from the [Docker Hub](https://hub.docker.com/))

- The Docker Hub is a cloud-based registry for software repositories or a library for Docker images. 
- It is divided into a `public` and a `private` area. 
	- The public area allows users to upload and share images with the community. 
	- Images uploaded to a private area of the registry are not publicly accessible. They can be shared within a company or with teams and acquaintances.

**Building a Docker Image:** 
- To create a Docker container, you first need to build a Docker image. An image is like a blueprint for your container.
-  You create a `Dockerfile`, which is a text file that contains instructions on how to build your image. This file specifies things like what base image to use, what dependencies to install, and what commands to run.
-  Once you have your `Dockerfile` ready, you use the `docker build` command to build your image. This command reads the Dockerfile and creates an image based on its instructions.

**Running a Docker Container:** 
- Once you have your Docker image, you can run it to create a container.
-  You use the `docker run` command to start a new container from your image. You can specify options like port mappings, volume mounts, environment variables, and more.
- Docker will create a container based on your image and start running your application inside it.

**Managing Docker:** 

```
	- You can manage your Docker containers using various commands.
    - `docker ps` - Lists all running containers.
    - `docker stop <container_id>` - Stops a running container.
    - `docker rm <container_id>` - Removes a stopped container.
    - `docker images` - Lists all Docker images on your system.
    - `docker rmi <image_id>` - Removes a Docker image.
    - `docker logs <container_id>` - Displays the logs of a container.
```

#### Linux Containers

- `LXC` is a virtualization technology that allows multiple isolated linux system to run on a single host.
- it uses resource isolation features such as `cgroups` and `namespace` .

**Install LXC**

```shell-session
cyber7ron@htb[/htb]$ sudo apt-get install lxc lxc-utils -y
```

**Creating an LXC Container**

```shell-session
cyber7ron@htb[/htb]$ sudo lxc-create -n linuxcontainer -t ubuntu
```
create ubuntu container named `linuxcontainer`.

**Managing LXC Container**

| Command                                       | Description                                                    |
| --------------------------------------------- | -------------------------------------------------------------- |
| `lxc-ls`                                      | List all existing containers                                   |
| `lxc-stop -n <container>`                     | Stop a running container.                                      |
| `lxc-start -n <container>`                    | Start a stopped container.                                     |
| `lxc-restart -n <container>`                  | Restart a running container.                                   |
| `lxc-config -n <container name> -s storage`   | Manage container storage                                       |
| `lxc-config -n <container name> -s network`   | Manage container network settings                              |
| `lxc-config -n <container name> -s security`  | Manage container security settings                             |
| `lxc-attach -n <container>`                   | Connect to a container.                                        |
| `lxc-attach -n <container> -f /path/to/share` | Connect to a container and share a specific directory or file. |

Here are 9 optional exercises to practice LXC:

- [ ]  Install LXC on your machine and create your first container.
- [ ] Configure the network settings for your LXC container.
- [ ] Create a custom LXC image and use it to launch a new container.
- [ ] Configure resource limits for your LXC containers (CPU, memory, disk space).
- [ ] Explore the `lxc-*` commands for managing containers.
- [ ] Use LXC to create a container running a specific version of a web server (e.g., Apache, Nginx).
- [ ] Configure SSH access to your LXC containers and connect to them remotely.
- [ ] Create a container with persistence, so changes made to the container are saved and can be reused.
- [ ] Use LXC to test software in a controlled environment, such as a vulnerable web application or malware.

***

***
