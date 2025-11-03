- [**A01:2021-Broken Access Control**](https://owasp.org/Top10/A01_2021-Broken_Access_Control/) - Insecure Direct Object Reference - (e.g. user has an ID number, each ID number is iterable, meaning an attacker can easily iterate up or down and get an adjacent object) *Addressed by*: Enforcing **deny-by-default** authorization, validating object reference ownership, and use indirect reference

- [**A02:2021-Cryptographic Failures**](https://owasp.org/Top10/A02_2021-Cryptographic_Failures/) - *Addressed by*: Using modern and secure encryption algorithms, and ensuring all sensitive data is sent via encrypted protocols (such as by preferring HTTPS or SSH to HTTP or Telnet). Also includes avoiding hardcoded secrets and rotating keys

- [**A03:2021-Injection**](https://owasp.org/Top10/A03_2021-Injection/) - *Addressed by*: Generally: Validating input, sanitizing the input, and then escaping the output. This means using an Allowlist for string character types, instead of blocklists, to prevent missing a key/vulnerable character, but then also generally using known, well-tested, and validated libraries to do things like escaping output for you and ensuring you understand how they work, as the dev

- [**A04:2021-Insecure Design**](https://owasp.org/Top10/A04_2021-Insecure_Design/) - *Addressed by*: Shifting the SDLC left by threat modeling, design reviews, and educating devs on secure design practices so the design is built with security in mind

- [**A05:2021-Security Misconfiguration**](https://owasp.org/Top10/A05_2021-Security_Misconfiguration/) - *Addressed by*: Hardening defaults, disabling unnecessary services, auditing IaC/container configs, and removing default accounts

- [**A06:2021-Vulnerable and Outdated Components**](https://owasp.org/Top10/A06_2021-Vulnerable_and_Outdated_Components/) - *Addressed by*: Monitoring code bases and dependencies for insecure components (should know all possible) and then regularly addressing them prioritized by a compound metric comprised of CVSS and EPSS tailored to a company's policies

- [**A07:2021-Identification and Authentication Failures**](https://owasp.org/Top10/A07_2021-Identification_and_Authentication_Failures/) - *Addressed by*: preferably no longer relying on outdated auth measures like secure access questions, but at a minimum should enforce MFA via Time-Based One-Time password (TOTP) apps (not through push notif, these can be leveraged to fatigue users into accepting malicious access), and proper session controls

- [**A08:2021-Software and Data Integrity Failures**](https://owasp.org/Top10/A08_2021-Software_and_Data_Integrity_Failures/) - *Addressed by*: ensuring that software and firmware updates are only accepted from trusted sources with verifications in place such as checksums, and also monitoring supply chain integrity.

- [**A09:2021-Security Logging and Monitoring Failures**](https://owasp.org/Top10/A09_2021-Security_Logging_and_Monitoring_Failures/) - *Addressed by*: ensuring that logging and monitoring is able to successfully detect breaches early. This includes logs that are well readable by humans, and are immutable, stored with redundancy, accessible through redundant access, and robust enough to catch all relevant events

- [**A10:2021-Server-Side Request Forgery**](https://owasp.org/Top10/A10_2021-Server-Side_Request_Forgery_%28SSRF%29/) - *Addressed by*: validating urls sent to internal services to prevent malicious requests, restricting internal network access and using allowlists for outbound requests.