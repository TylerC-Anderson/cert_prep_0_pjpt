State
- You have a normal user shell.
- No sudo or elevated rights confirmed.

Goal → Escalate to higher privilege or pivot laterally.

### IF YOU SEE:

If `sudo -l` works →
    → Check allowed commands
    → Move to 3.1_sudo-access

If SUID/SGID binaries exist →
    → Check for GTFOBins matches
    → [[2_Studies/Courses/Current/CERTPREP - PJPT-Practical Jr Pen Tester/Practical Ethical Hacking/4_Privilege_Escalation/GTFO Bins]]

If crontab / timers writable →
    → Privilege escalation vector

If config files contain passwords →
    → Extract creds → reuse locally or elsewhere

If kernel version is old or unusual →
    → Kernel exploit potential (慎 use)

If SSH keys readable →
    → Lateral movement possible

### Core Tools:

- [[2_Studies/Courses/Current/CERTPREP - PJPT-Practical Jr Pen Tester/Practical Ethical Hacking/4_Privilege_Escalation/LinPEAS, WinPEAS, & SharPEAS]]
- [[2_Studies/Courses/Current/CERTPREP - PJPT-Practical Jr Pen Tester/Practical Ethical Hacking/4_Privilege_Escalation/GTFO Bins]]

### Secondary Actions:

- [[2_Studies/Courses/Current/CERTPREP - PJPT-Practical Jr Pen Tester/Practical Ethical Hacking/3_Exploitation/Hashcracking]]
- [[2_Studies/Courses/Current/CERTPREP - PJPT-Practical Jr Pen Tester/Practical Ethical Hacking/5_Post-Exploitation/Hosted Payloads & File Transfers]]

### Supplemental / Contextual

- [[2_Studies/Courses/Current/CERTPREP - PJPT-Practical Jr Pen Tester/Practical Ethical Hacking/5_Post-Exploitation/Pivoting]]
