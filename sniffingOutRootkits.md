# Checking Hidden Processes, Rootkits in Linux using Unhide Tool.

Linux is a secure Operating System with little malware, but users can get compromised through Rootkit attacks.

Rootkit malware is a collection of software designed to give malicious actors control of a computer network or application. 

Once activated, the malicious program sets up a backdoor exploit and may deliver additional malware, such as ransomware, bots, keyloggers or trojans. 

Can be used to perform a [DDoS](https://www.comptia.org/content/guides/what-is-a-ddos-attack-how-it-works) (Distributed Denial of Service) attack.

Rootkit malware can run undetected for a very long period of time.

## Types of Rootkit malware:

**Kernel Mode Rootkits (Ring 0)** 

* Sophisticated malware which can add pieces of code to an Operating System, target the kernel system.
* Hard to detect
* Examples include: [Spicy Hot Pot](https://www.crowdstrike.com/blog/spicyhotpotrootkitexplained/#:~:text=Spicy%20Hot%20Pot%20is%20a,ensure%20it%20can%20remain%20updated.)

**Virtualized rootkits (Ring 1)**

* Boots up before the actual OS boots 
* Hosts the main OS as a virtual machine
* It's able to intercept hardware calls by the main OS.
* Really difficult to detect as it doesn't modify the kernel to affect the OS.


**Bootloader Rootkits (Ring 2)**

* Target the system's **MBR** (Master Boot Record) *first code executed when starting up a computer*,  or **VBR** (Volume Boot Record) *contains the code needed to initiate the boot process*
* Replaces the system's boot code with a malicious one, activating the rootkit before the OS loads.
* Example:
[BlackLotus Bootkit](https://www.bleepingcomputer.com/news/security/malware-dev-claims-to-sell-new-blacklotus-windows-uefi-bootkit/) 


**Memory Rootkits (Ring 2)**

* Hide in the system's RAM (Random Access Memory)
* Use the system's resources to perform malicious background activities
* Reduce system's performance
* Can be cleared out by rebooting.


**User Mode Rootkits (Ring 3)**

* Modify the behavior of application programming interfaces.
* Can display false information to administrators to hide their presence.
* Leave breadcrumbs that trigger antivirus and rootkit remover alerts since they target applications not OS.
* Easier to remove.
* Example:
[AFX](https://en.wikipedia.org/wiki/AFX_Windows_Rootkit_2003)


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
One major challenge when checking for Rootkit activity is distinguishing false positives from malicious activities.

Unhide can output false positives as seen above, with `...maybe a transitory process`.

One can run the test several times for confirmation.

## 3. Comparing `/proc` and `/bin/ps`

In linux, the `/proc` directory is a virtual file system created when the system boots, showing all the system's running processes.

It's dissolved when the system shuts down.

A snippet of the `/proc` directory:

```
-sh-4.2$ cd /proc/
-sh-4.2$ ls -lh
total 0
dr-xr-xr-x  9 root           root              0 Sad 10 13:52 1
dr-xr-xr-x  9 root           root              0 Sad 17 13:07 10
dr-xr-xr-x  9 root           root              0 Sad 17 13:07 100
dr-xr-xr-x  9 root           root              0 Sad 17 13:07 101
dr-xr-xr-x  9 root           root              0 Sad 17 13:07 102
dr-xr-xr-x  9 root           root              0 Sad 17 13:07 10324
dr-xr-xr-x  9 root           root              0 Sad 17 13:07 104
dr-xr-xr-x  9 root           root              0 Sad 17 13:07 10441
dr-xr-xr-x  9 root           root              0 Sad 17 13:07 105
dr-xr-xr-x  9 root           root              0 Sad 17 13:07 10570
dr-xr-xr-x  9 root           root              0 Sad 17 13:07 106
dr-xr-xr-x  9 root           root              0 Sad 17 13:07 107
dr-xr-xr-x  9 root           root              0 Sad 17 13:07 10836
dr-xr-xr-x  9 root           root              0 Sad 17 13:07 10845
dr-xr-xr-x  9 karegapau      karegapau         0 Sad 17 13:07 10850
dr-xr-xr-x  9 karegapau      karegapau         0 Sad 17 13:07 10851
dr-xr-xr-x  9 root           root              0 Sad 17 13:07 109
dr-xr-xr-x  9 root           root              0 Sad 17 13:07 10928

```

**N.B** `dr-xr-xr-x` shows the file permissions for the running process. An example of a PID is `10928`.

The `ps` command is also used to show all running processes in Linux.

```
-sh-4.2$ ps
  PID TTY          TIME CMD
22834 pts/12   00:00:00 sh
23648 pts/12   00:00:00 ps

```
Using `ps` command alone only shows processes currently running in my account from my logged in terminal (TTY)

For a network with many workstations (eg. servers) , `ps aux` shows a comprehensive report of all processes running in the system, regardless of where they are being executed from.

```
-sh-4.2$ sudo ps aux
USER       PID %CPU %MEM    VSZ   RSS TTY      STAT START   TIME COMMAND
root         1  0.0  0.0 191976  3800 ?        Ss   Sad10   3:41 /usr/lib/systemd/systemd --switched-root --system --deserialize 22
root         2  0.0  0.0      0     0 ?        S    Sad10   0:01 [kthreadd]
root         4  0.0  0.0      0     0 ?        S<   Sad10   0:00 [kworker/0:0H]
root         6  0.0  0.0      0     0 ?        S    Sad10   1:55 [ksoftirqd/0]
root         7  0.0  0.0      0     0 ?        S    Sad10   0:12 [migration/0]
root         8  0.0  0.0      0     0 ?        S    Sad10   0:00 [rcu_bh]
root         9  2.5  0.0      0     0 ?        S    Sad10 412:11 [rcu_sched]
root        10  0.0  0.0      0     0 ?        S<   Sad10   0:00 [lru-add-drain]
root        11  0.0  0.0      0     0 ?        S    Sad10   0:05 [watchdog/0]
root        12  0.0  0.0      0     0 ?        S    Sad10   0:05 [watchdog/1]
root        13  0.0  0.0      0     0 ?        S    Sad10   0:06 [migration/1]
root        14  0.0  0.0      0     0 ?        S    Sad10   0:46 [ksoftirqd/1]
root        16  0.0  0.0      0     0 ?        S<   Sad10   0:00 [kworker/1:0H]
root        18  0.0  0.0      0     0 ?        S    Sad10   0:04 [watchdog/2]
root        19  0.0  0.0      0     0 ?        S    Sad10   0:10 [migration/2]
root        20  0.0  0.0      0     0 ?        S    Sad10   6:34 [ksoftirqd/2]
root        22  0.0  0.0      0     0 ?        S<   Sad10   0:00 [kworker/2:0H]
root        23  0.0  0.0      0     0 ?        S    Sad10   0:04 [watchdog/3]
root        24  0.0  0.0      0     0 ?        S    Sad10   0:05 [migration/3]
root        25  0.0  0.0      0     0 ?        S    Sad10   4:03 [ksoftirqd/3]
root        27  0.0  0.0      0     0 ?        S<   Sad10   0:00 [kworker/3:0H]
...
```
In a healthy system, both `/proc` and `/bin/ps` should output similar results.

We can therefore use Unhide to compare both processes, using `sudo unhide proc -v` command.

```
-sh-4.2$  sudo unhide proc -v
Unhide 20130526
Copyright © 2013 Yago Jesus & Patrick Gouin
License GPLv3+ : GNU GPL version 3 or later
http://www.unhide-forensics.info

NOTE : This version of unhide is for systems using Linux >= 2.6 

Used options: verbose 
[*]Searching for Hidden processes through /proc stat scanning

-sh-4.2$ 

```
An empty output shows no anomalies after comparison has been done.

For a more comprehensive `/proc` test:

`sudo unhide procall -v `

```
-sh-4.2$ sudo unhide procall -v 
Unhide 20130526
Copyright © 2013 Yago Jesus & Patrick Gouin
License GPLv3+ : GNU GPL version 3 or later
http://www.unhide-forensics.info

NOTE : This version of unhide is for systems using Linux >= 2.6 

Used options: verbose 
[*]Searching for Hidden processes through /proc stat scanning

[*]Searching for Hidden processes through /proc chdir scanning

[*]Searching for Hidden processes through /proc opendir scanning

[*]Searching for Hidden thread through /proc/pid/task readdir scanning

```

## 4. Performing a reverse scan on ps threads.

Unhide can also be used to verify that all the processes listed with `ps` are valid and can be found in `procfs` (Proc Filesystem).

It's a relatively quick scan.

`sudo unhide reverse`

```
-sh-4.2$ sudo unhide reverse
[sudo] password for cyndiekamau: 
Unhide 20130526
Copyright © 2013 Yago Jesus & Patrick Gouin
License GPLv3+ : GNU GPL version 3 or later
http://www.unhide-forensics.info

NOTE : This version of unhide is for systems using Linux >= 2.6 

Used options: 
[*]Searching for Fake processes by verifying that all threads seen by ps are also seen by others

-sh-4.2$ 

```

## 5. Comparing `/bin/ps` with System Calls

It's a very comprehensive scan, as it involves comparing all the `ps` processes with valid system calls.

`sudo unhide sys`

```
-sh-4.2$ sudo unhide sys 
[sudo] password for cyndiekamau: 
Unhide 20130526
Copyright © 2013 Yago Jesus & Patrick Gouin
License GPLv3+ : GNU GPL version 3 or later
http://www.unhide-forensics.info

NOTE : This version of unhide is for systems using Linux >= 2.6 

Used options: 
[*]Searching for Hidden processes through getpriority() scanning

[*]Searching for Hidden processes through getpgid() scanning

[*]Searching for Hidden processes through getsid() scanning

[*]Searching for Hidden processes through sched_getaffinity() scanning

[*]Searching for Hidden processes through sched_getparam() scanning

[*]Searching for Hidden processes through sched_getscheduler() scanning

[*]Searching for Hidden processes through sched_rr_get_interval() scanning

[*]Searching for Hidden processes through kill(..,0) scanning

[*]Searching for Hidden processes through  comparison of results of system calls

-sh-4.2$ 

```

Advanced popular, open source Rootkit detection tools include [Rkhunter](https://www.kali.org/tools/rkhunter/) and [Chkrootkit Security Scanner.](https://www.kali.org/tools/chkrootkit/) 
