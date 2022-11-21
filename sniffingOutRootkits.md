# Checking Hidden Processes in Linux using Unhide Tool.

Linux is a secure Operating System with little malware, but users can get compromised through Rootkit attacks.

Rootkit malware is a collection of software designed to give malicious actors control of a computer network or application. 

Once activated, the malicious program sets up a backdoor exploit and may deliver additional malware, such as ransomware, bots, keyloggers or trojans. 

Rootkit malware can run undetected for a very long period of time.

## Types of Rootkit malware:

* Firmware Rootkits
* Kernel Mode Rootkits
* Bootloader Rootkits
* Virtualized rootkits
* User Mode Rootkits
* Memory Rootkits

As a Linux System Admin it is crucial to constantly check for any hidden processes running in the network, to confirm there are no malicious backdoor processes running undetected.

One can use the **Unhide** tool for analysis.

## 1. Installation

**Ubuntu**

`sudo apt-get install unhide`

**CentOS 7**

`sudo yum install unhide`

Successful installation should show the following:

```
Downloading packages:
unhide-20130526-1.el7.x86_64.rpm                                                                                |  63 kB  00:00:01     
Running transaction check
Running transaction test
Transaction test succeeded
Running transaction
  Installing : unhide-20130526-1.el7.x86_64                                                                                        1/1 
  Verifying  : unhide-20130526-1.el7.x86_64                                                                                        1/1 

Installed:
  unhide.x86_64 0:20130526-1.el7                                                                                                       

Complete!

```

One can also check if Unhide package is present through the `rpm` command:

```
-sh-4.2$ rpm -qa | grep unhide
unhide-20130526-1.el7.x86_64
-sh-4.2$ 

```

## 2. Using Unhide to Bruteforce Process IDs

In linux, once a process is launched its given a unique PID (Process ID).

PIDs are therefore used to pinpoint which processes are running in the system.

The first basic system check is bruteforcing all PIDs to ensure none of them are hidden from the user.

Use the following command:

```
sudo unhide brute -d 
```

The `-d` option doubles the test to cut down on the number of false positives reported.

Output can be as follows;

```
-sh-4.2$ sudo unhide brute -d
Unhide 20130526
Copyright © 2013 Yago Jesus & Patrick Gouin
License GPLv3+ : GNU GPL version 3 or later
http://www.unhide-forensics.info

NOTE : This version of unhide is for systems using Linux >= 2.6 

Used options: brutesimplecheck 
[*]Starting scanning using brute force against PIDS with fork()

Found HIDDEN PID: 49565
	Cmdline: "<none>"
	Executable: "<no link>"
	"<none>  ... maybe a transitory process"

[*]Starting scanning using brute force against PIDS with pthread functions

Found HIDDEN PID: 45762
	Cmdline: "<none>"
	Executable: "<no link>"
	"<none>  ... maybe a transitory process"

```



