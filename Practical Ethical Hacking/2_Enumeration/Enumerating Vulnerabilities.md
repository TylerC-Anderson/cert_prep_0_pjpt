
## Quick-Use

**Commands**:

- `nessus` - quickstart clickpath:
    - Start Nessus Scanner: `/bin/systemctl start nessusd.service`
    - Then go to https://NESSUS_HOSTNAME_OR_IP:8834/ to configure the scanner:
        - My Scans -> new scan
        - Basic Network Scan or Advanced Scan:
            - Basic Network Scan:
                - Basic > General -> Fill out target info
                - Basic > Schedule -> Can make recurring or schedule one scan ahead of time
                - Discovery -> Port scan top 1k, all ports, or custom ranges
                - Assessment -> Scan Type: Default, Known Web Vulns, All web vulns (quick or complext)
                - Report -> Reporting options, can usu. leave as default
                - Advanced -> Parallelism options, can usu. leave as default
            - Advanced Scan:
                - Much the same as above, EXCEPT:
                    - Discovery > Host Discovery -> can choose to ping remote host or not. Can ping to dicover ARP, TCP, ICMP, UDP. Can ping for Fragile devices (e.g. printers)
                    - Discovery > Port Scanning -> Syn scanning, UDP scanning
                    - Discovery > Service Discovery -> Good, can usu. leave as default
                    - Assessment > Brute Force -> Can check default cred and accounts, brute force logins, Hydra login cracking, empty passwords, passwords set to `password`
                    - Assessment > Web Applications -> User Agent changes, where to start a webcrawl from
    - After scans complete, we can export Nessus reports to auto-parsers that will convert it to a CSV or XLSX file, and some that will even parse it to a full pentest report (`dradis.com`, for example). **HOWEVER** any scan results MUST BE validated before finalizing to a report. Screenshots and receipts, **ALWAYS**.

*Tools*:
- `nessus` - widely used vulnerability scanner that can automatically detect hosts on a network and identify various operating systems, services, and configuration errors. Nessus supports multiple scanning strategies, including port scanning, operating system fingerprinting, weak password detection, and more.
- `google.com`
- `Exploit-db.com` - Website with exploits POC code snippets
- `searchsploit` - good for offline

## General

**Objectives**:
1. Systematically gather information on prime targets for exploitation, and evaluate them based on the following criteria:
    - *ease of exploitation*
    - *detectability/stealth/operational risk*
    - *scope of impact*
    - *Post-Exploitation value* (i.e. can you pivot or PrivEsc from it).

2. Then, generate a ranked shortlist of the actionable targets (*and their vulns*) that balance those criteria.
3. Then, find exploits that have been published for those vulns



**Overview**: Typically, the juiciest targets are:
- [[2_Studies/Courses/Current/CERTPREP - PJPT-Practical Jr Pen Tester/Practical Ethical Hacking/2_Enumeration/Enumerating HTTP and HTTPS|Port 80]]
- [[2_Studies/Courses/Current/CERTPREP - PJPT-Practical Jr Pen Tester/Practical Ethical Hacking/2_Enumeration/Enumerating HTTP and HTTPS|Port 443]]
- [[2_Studies/Courses/Current/CERTPREP - PJPT-Practical Jr Pen Tester/Practical Ethical Hacking/2_Enumeration/Enumerating SMB|Port 139/445]]




## Glossary