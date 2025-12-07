State
- No shell.
- You may or may not have network access.
- Target OS not yet confirmed.

Goal → Identify Linux targets and initial entry points.

### IF YOU SEE:

If scanning shows Linux-like services (SSH, HTTP, FTP, NFS) →
    → Likely Linux host → enumerate service-specific attack surface

If port 22 (SSH) is open →
    → Password/key-based access likely
    → Look for creds or username leaks

If HTTP/HTTPS is exposed →
    → Likely web → potential RCE / file upload → shell later

If NFS (2049) is exposed →
    → Check for anonymous mounts
    → May lead directly to credential files or shell access

If banners reference Linux distros, kernel versions →
    → Note versioning for later privesc

### Core Tools:

- [[2_Enumeration/nmap - Active Scanning]]
- [[2_Enumeration/0_General - Enumeration Gathering]]
- [[2_Enumeration/Enumerating HTTP and HTTPS]]
- [[2_Enumeration/Enumerating SSH]]

### Supplemental / Contextual

- [[Networking Review]]
- [[Attack Methodology & Strategy]]
- [[Ethical Hacker Methodology]]

- [[1_Reconnaissance/0_General - Reconnaissance & Intel]]
- [[1_Reconnaissance/Website Reconnaissance]]
- [[2_Enumeration/Investigating Breached Credentials]]
