State
- You have internal network access.
- No valid credentials.
- You can query SMB, LDAP, or Kerberos anonymously.

Goal → Obtain your first credential.

### IF YOU SEE:

If SMB port 445 is open AND signing is disabled →
    → Relay attacks viable  
    → Check: [[2_Studies/Courses/Current/CERTPREP - PJPT-Practical Jr Pen Tester/Practical Ethical Hacking/3_Exploitation/Active Directory/SMB Relay]]

If LLMNR/NBT-NS traffic is present →
    → Poison responses to capture hashes  
    → Check: [[2_Studies/Courses/Current/CERTPREP - PJPT-Practical Jr Pen Tester/Practical Ethical Hacking/3_Exploitation/LLMNR Poisoning]]

If IPv6 is active →
    → Rogue RA / DHCPv6 attacks possible  
    → Check: [[2_Studies/Courses/Current/CERTPREP - PJPT-Practical Jr Pen Tester/Practical Ethical Hacking/3_Exploitation/IPv6 Attacks]]

If LDAP anonymous bind works →
    → Enumerate users, groups, computers  
    → Feed usernames into spraying later

If Kerberos is present (port 88) →
    → AD confirmed  
    → But AS-REP roasting requires valid usernames

If usernames are recovered →
    → Begin spraying  
    → Check: [[2_Studies/Courses/Current/CERTPREP - PJPT-Practical Jr Pen Tester/Practical Ethical Hacking/3_Exploitation/Credential Stuffing & Password Spraying]]  
    → Or brute if conditions allow: [[2_Studies/Courses/Current/CERTPREP - PJPT-Practical Jr Pen Tester/Practical Ethical Hacking/3_Exploitation/Brute-Force Attacks]]

If responder/relay captures hashes →
    → Crack them  
    → [[2_Studies/Courses/Current/CERTPREP - PJPT-Practical Jr Pen Tester/Practical Ethical Hacking/3_Exploitation/Hashcracking]]

### Core Tools:

- [[2_Studies/Courses/Current/CERTPREP - PJPT-Practical Jr Pen Tester/Practical Ethical Hacking/3_Exploitation/LLMNR Poisoning]]
- [[2_Studies/Courses/Current/CERTPREP - PJPT-Practical Jr Pen Tester/Practical Ethical Hacking/3_Exploitation/Active Directory/SMB Relay]]
- [[2_Studies/Courses/Current/CERTPREP - PJPT-Practical Jr Pen Tester/Practical Ethical Hacking/3_Exploitation/IPv6 Attacks]]

- [[2_Studies/Courses/Current/CERTPREP - PJPT-Practical Jr Pen Tester/Practical Ethical Hacking/2_Enumeration/Enumerating SMB]]
- [[2_Studies/Courses/Current/CERTPREP - PJPT-Practical Jr Pen Tester/Practical Ethical Hacking/2_Enumeration/Enumerating Vulnerabilities]]

### Secondary Actions (when usernames exist)

- [[2_Studies/Courses/Current/CERTPREP - PJPT-Practical Jr Pen Tester/Practical Ethical Hacking/3_Exploitation/Credential Stuffing & Password Spraying]]
- [[2_Studies/Courses/Current/CERTPREP - PJPT-Practical Jr Pen Tester/Practical Ethical Hacking/3_Exploitation/Brute-Force Attacks]]
- [[2_Studies/Courses/Current/CERTPREP - PJPT-Practical Jr Pen Tester/Practical Ethical Hacking/3_Exploitation/Hashcracking]]

### Supplemental / Contextual

- [[3_Exploitation/Active Directory/Attacking AD]]
- [[2_Studies/Courses/Current/CERTPREP - PJPT-Practical Jr Pen Tester/Practical Ethical Hacking/2_Enumeration/Investigating Breached Credentials]]
