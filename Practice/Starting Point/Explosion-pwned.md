## Box: [Explosion] — Started: 2025-10-05 22:10

**IP/Host:** `HTB Attackbox`  |  **Status:** `Owned 25-10-05`

### TL;DR

Initial: `xfreerdp /u:username /p:password /v:ip_address`. 
PrivEsc: Flag was on desktop after connection.

*Cumulative Impact Overview*:
- `xfreerdp` RDP CLI client lab

### Exploit Lifecycle

1. *Validation & Recon*: HTB CTF
2. *Enumeration & Scanning*: `nmap -sV IPADDR`
3. *Findings & Exploits*:
    - `xfreerdp /u:administrator /p: /v:IPADDR` - allowed remote desktop access to box
4. *PrivEsc*:
    - Flag
5. *Post-Exploitation*:

*Proof — path to flags/screenshot of boxpwn*:

![[2_Studies/Courses/Current/CERTPREP - PJPT-Practical Jr Pen Tester/Practice/Starting Point/z_attachments/Pasted image 20251005225127.png]]

