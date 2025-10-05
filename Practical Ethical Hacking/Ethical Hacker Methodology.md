## 5 stages of Ethical Hacking or: The Attack Lifecycle

1. **RECONNAISSANCE** (aka *information gathering*)
    - Passive (**OSINT**, **Physical Observation**)
        - Google Searches
            - Company information such as key employees/leadership, tech stack
            - Sensitive data such as Pictures of an employee and their badge, or Username and Password
            - Building Pictures
        - Employee Social Media (phishing information)
            - LinkedIn
            - Twitter
            - YouTube account for sensitive internal company data
    - Active -> **Scanning and Enumeration**
        - *Scanning* - Active scanners searching for open ports, OS information
            - Scanners include:
                - `namp`
                - `nessus`
                - `nikto`
        - After scanning you then perform *Enumeration* - using discovered information to try and discover more information by prying into them a bit further
            - Open port that is insecured because it has an unpatched export or has no authentication credentials required
2. **ENUMERATION**
    - *Scanning* - Active scanners searching for open ports, OS information
            - Scanners include:
                - `namp`
                - `nessus`
                - `nikto`
    - *Enumeration* - using discovered information to try and discover more information by prying into them a bit further to see if they are **Vulnerable to Exploitation**
        - Performed after scanning
        - E.g. During scanning you find an Open port, so you check to see if it is unsecured because it has an unpatched exploit or has no authentication credentials required
3. **EXPLOITATION**
    - *Gaining Access* once target is Reconned, scanned, and enumerated
    - Once access is gained, *take a look around* -> loop back to **Scanning and Enumeration** again
4. **PRIVILEGE ESCALATION**
    - *Estabilishing Persistence* Once Access is gained, work to maintain access, or stabilize it
5. **POST-EXPLOITATION**
    - Delete logs
    - delete malware that is not obfuscated
    - delete accounts used for testing
    - clean up after yourself





