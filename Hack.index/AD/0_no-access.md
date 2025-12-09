State
- You have no internal access yet OR you just landed in the network.
- You need to identify hosts, services, and whether AD exists.

Goal → Discover services, confirm AD, identify first footholds.

### IF YOU SEE:

If you see port 88 (Kerberos) →
    → AD confirmed → Move to 1_anon-access  
    → Review: [[3_Exploitation/Active Directory/Active Directory Background]]

If you see port 389/636 (LDAP) →
    → Try anon bind later (in 1_anon-access)  
    → Note LDAP server hostnames for AD mapping

If you see port 445 (SMB) →
    → Check anonymous/null session later  
    → Useful: SMB often visible even before other AD indicators

If scanning shows Windows hosts or DNS names like *.local / *.corp / *.internal →
    → Likely AD domain → Note naming conventions  
    → Check: [[1_Reconnaissance/Investigating Email Addresses]]

If HTTP/HTTPS banners reference SharePoint, Outlook, OWA, Teams, or Azure AD →
    → Strong AD presence  
    → Confirm by enumerating the infrastructure

### Core Tools:

- [[2_Enumeration/nmap - Active Scanning]]
- [[2_Enumeration/0_General - Enumeration Gathering]]
- [[2_Enumeration/Enumerating HTTP and HTTPS]]
- [[2_Enumeration/Enumerating SSH]]
- [[SMB Relay]]
- [[Enumerating SMB]]

### Supplemental / Contextual

- [[Networking Review]]
- [[Attack Methodology & Strategy]]
- [[Ethical Hacker Methodology]]

- [[1_Reconnaissance/0_General - Reconnaissance & Intel]]
- [[1_Reconnaissance/Website Reconnaissance]]
- [[1_Reconnaissance/Investigating Email Addresses]]
- [[2_Enumeration/Investigating Breached Credentials]]
