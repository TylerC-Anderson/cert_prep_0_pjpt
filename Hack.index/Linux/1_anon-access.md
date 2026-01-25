State
- Network-level interaction with services.
- No shell.
- No valid credentials.

Goal → Turn unauthenticated access into a shell.

### IF YOU SEE:

If SSH prompts for password →
    → Username guessing / credential reuse possible
    → Prepare for brute/spray later

If FTP allows anonymous login →
    → Look for writable directories or credential files

If NFS allows anonymous mount →
    → Mount share → search for creds / SSH keys

If HTTP app allows file upload →
    → Upload webshell or payload
    → Leads to user shell

If HTTP endpoint vulnerable to command injection →
    → Direct shell potential
    → [[3_Exploitation/Command Injection]]

If application config files exposed →
    → Extract creds → reuse for SSH / sudo

### Core Tools:

- [[2_Studies/Courses/Current/CERTPREP - PJPT-Practical Jr Pen Tester/Practical Ethical Hacking/2_Enumeration/Enumerating HTTP and HTTPS]]
- [[2_Studies/Courses/Current/CERTPREP - PJPT-Practical Jr Pen Tester/Practical Ethical Hacking/3_Exploitation/Manual Exploitation]]
- [[2_Studies/Courses/Current/CERTPREP - PJPT-Practical Jr Pen Tester/Practical Ethical Hacking/3_Exploitation/Gaining Shell Access]]

### Secondary Actions:

- [[2_Studies/Courses/Current/CERTPREP - PJPT-Practical Jr Pen Tester/Practical Ethical Hacking/3_Exploitation/Brute-Force Attacks]]
- [[2_Studies/Courses/Current/CERTPREP - PJPT-Practical Jr Pen Tester/Practical Ethical Hacking/3_Exploitation/Credential Stuffing & Password Spraying]]
- [[2_Studies/Courses/Current/CERTPREP - PJPT-Practical Jr Pen Tester/Practical Ethical Hacking/3_Exploitation/Hashcracking]]

### Supplemental / Contextual

- [[2_Studies/Courses/Current/CERTPREP - PJPT-Practical Jr Pen Tester/Practical Ethical Hacking/3_Exploitation/Burp Suite]]
