State
- You have a valid domain user credential.
- User is low privilege (Domain Users only).

Goal → Convert basic user access into actionable escalation.

### IF YOU SEE:

If LDAP bind works →
    → Dump domain structure using LDAP tools  
    → [[2_Enumeration/Post-Compromise Enumeration/Active Directory/Ldapdomaindump Enumeration]]

If you can run BloodHound collectors →
    → Identify shortest escalation path  
    → [[2_Enumeration/Post-Compromise Enumeration/Active Directory/Bloodhound and Plumhound Enumeration]]

If you see SPNs for any account →
    → Attempt kerberoasting  
    → [[2_Enumeration/Post-Compromise Enumeration/Active Directory/Kerberoasting]]  
    → Then crack: [[3_Exploitation/Hashcracking]]

If you can read shares (SYSVOL/NETLOGON) →
    → Look for GPP passwords or credential artifacts  
    → [[3_Exploitation/Post-Compromise Exploitation/GPP Attack AKA cPassword Attacks]]

If multiple hosts accept these credentials →
    → Attempt lateral login and environment enumeration  
    → Prepares path to local admin or special-priv

If hashes, passwords, or scripts found →
    → Crack or reuse  
    → [[3_Exploitation/Hashcracking]]

### Core Tools:

- [[2_Enumeration/Post-Compromise Enumeration/Active Directory/Bloodhound and Plumhound Enumeration]]
- [[2_Enumeration/Post-Compromise Enumeration/Active Directory/Ldapdomaindump Enumeration]]
- [[2_Enumeration/Post-Compromise Enumeration/Active Directory/Kerberoasting]]

### Secondary Actions:

- [[3_Exploitation/Post-Compromise Exploitation/GPP Attack AKA cPassword Attacks]]
- [[3_Exploitation/Credential Stuffing & Password Spraying]]
- [[3_Exploitation/Hashcracking]]

### Supplemental / Contextual

- [[5_Post-Exploitation/Hosted Payloads & File Transfers]]
