## Box: [Preignition] — Started: 2025-10-05 22:10

**IP/Host:** `HTB Attackbox`  |  **Status:** `Owned: 25-10-05`

### TL;DR

Initial: `http://IPADDR/admin.php`. PrivEsc: `admin:admin` on login screen for `admin.php`.

*Cumulative Impact Overview*:
- `dirbuster` practice lab

### Exploit Lifecycle

1. *Validation & Recon*: HTB CTF
2. *Enumeration & Scanning*:
    - `nmap -sV IPADDR` - found nginx server on port 80
    - `gobuster dir -x php -u http://IPADDR -w /usr/share/wordlists/dirbuster/directory-list-2.3-small.txt` - revealed an `admin.php` page was available
3. *Findings & Exploits*:
    - navigating to `http://IPADDR/admin.php` in the browser showed a login screen. attempting username:password combo of `admin:admin` let me in
4. *PrivEsc*:
5. *Post-Exploitation*:
    - Flag displayed on screen immediately after auth 

*Proof — path to flags/screenshot of boxpwn*:

![[2_Studies/Courses/Current/CERTPREP - PJPT-Practical Jr Pen Tester/VulnBoxes/Hack The Box/Starting Point/z_attachments/Pasted image 20251005230842.png]]



### Commands Learned or Commands Used
```bash
gobuster dir -x php -u http://IPADDR -w /usr/share/wordlists/chosen_list
```

##### *Example Dirbuster output*

```bash
┌─[us-starting-point-vip-1-dhcp]─[10.10.14.20]─[htb-mp-954215@htb-eldyftujfu]─[~]
└──╼ [★]$ gobuster dir -x php -u http://10.129.32.232 -w /usr/share/wordlists/dirbuster/directory-list-2.3-small.txt 
===============================================================
Gobuster v3.6
by OJ Reeves (@TheColonial) & Christian Mehlmauer (@firefart)
===============================================================
[+] Url:                     http://10.129.32.232
[+] Method:                  GET
[+] Threads:                 10
[+] Wordlist:                /usr/share/wordlists/dirbuster/directory-list-2.3-small.txt
[+] Negative Status codes:   404
[+] User Agent:              gobuster/3.6
[+] Extensions:              php
[+] Timeout:                 10s
===============================================================
Starting gobuster in directory enumeration mode
===============================================================
/admin.php            (Status: 200) [Size: 999]
Progress: 50546 / 175330 (28.83%)^C
[!] Keyboard interrupt detected, terminating.
Progress: 50927 / 175330 (29.05%)
===============================================================
Finished
===============================================================

```