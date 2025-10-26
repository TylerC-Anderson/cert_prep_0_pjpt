## Box: [Fawn] — Started: 2025-10-05 20:10

**IP/Host:** `HTB Attackbox`  |  **Status:** `Owned: 25-10-05`

### TL;DR

Initial: `anonymous@IPADDR`. Flag Cap: `ftp> get flag.txt`.

*Cumulative Impact Overview*:
- FTP Learning Lab

### Exploit Lifecycle

1. *Validation & Recon*: HTB CTF
2. *Enumeration & Scanning*: 
    - `nmap -sV IPADDR` - found FTP service up (port 21)
    - `nmap -O IPADDR` - found OS of box
3. *Findings & Exploits*: 
    - `ftp anonymous@IPADDR` - Anon login successful
4. *PrivEsc*: N/A
5. *Post-Exploitation*: 
    - `ftp> get flag.txt` - Downloaded flag to home directory, to then cat out the flag string

*Proof — path to flags/screenshot of boxpwn*:

![[Pasted image 20251005205523.png]]

### Commands Learned
```bash
nmap -O IPADDR         # OS information scan
ftp anonymous@IPADDR   # anon login
ftp> get flag.txt      # while in FTP CLI
```

