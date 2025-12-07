State
- You have multiple users and/or multiple host footholds.
- You need to choose the best escalation path.

Goal → Chain access using the lowest-friction route.

### IF YOU SEE:

If BloodHound shortest path chains multiple accounts or hosts →
    → Use that chain  
    → [[2_Enumeration/Post-Compromise Enumeration/Active Directory/Bloodhound and Plumhound Enumeration]]

If multiple users share passwords →
    → Try credential reuse  
    → [[3_Exploitation/Credential Stuffing & Password Spraying]]

If you have many hashes →
    → Crack in bulk  
    → [[3_Exploitation/Hashcracking]]

If multiple hosts provide partial admin access →
    → Lateral pivot  
    → [[5_Post-Exploitation/Pivoting]]

If scripts/services across machines reveal creds →
    → Consolidate → choose strongest credential  
    → [[5_Post-Exploitation/Hosted Payloads & File Transfers]]

### Core Tools:

- [[2_Enumeration/Post-Compromise Enumeration/Active Directory/Bloodhound and Plumhound Enumeration]]
- [[3_Exploitation/Credential Stuffing & Password Spraying]]
- [[3_Exploitation/Hashcracking]]

### Chain & Pivot Ops

- [[5_Post-Exploitation/Pivoting]]
- [[5_Post-Exploitation/Hosted Payloads & File Transfers]]
- [[5_Post-Exploitation/Persistence & Maintaining Access]]
