State
- You control multiple users and/or multiple Linux hosts.

Goal → Chain access, reuse creds, escalate everywhere possible.

### IF YOU SEE:

If SSH keys reused →
    → Lateral movement
    → [[5_Post-Exploitation/Pivoting]]

If same password reused →
    → Spray across hosts
    → [[3_Exploitation/Credential Stuffing & Password Spraying]]

If backups shared across hosts →
    → Dump creds → escalate

If admin access differs between hosts →
    → Escalate on weakest first

### Core Tools:

- [[5_Post-Exploitation/Pivoting]]
- [[3_Exploitation/Hashcracking]]

### Supplemental / Contextual

- [[5_Post-Exploitation/Hosted Payloads & File Transfers]]
- [[5_Post-Exploitation/Persistence & Maintaining Access]]
