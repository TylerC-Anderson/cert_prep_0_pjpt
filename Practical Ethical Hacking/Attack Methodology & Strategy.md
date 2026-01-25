## Full methodology workflow

1. Run [[2_Studies/Courses/Current/CERTPREP - PJPT-Practical Jr Pen Tester/Practical Ethical Hacking/2_Enumeration/nmap - Active Scanning|nmap]] against target IP addr
2. 

### Post-Compromise Attack Strategy, pre-domain compromise:
Once you get an account:
- search for quick wins:
    - [[2_Studies/Courses/Current/CERTPREP - PJPT-Practical Jr Pen Tester/Practical Ethical Hacking/2_Enumeration/Post-Compromise Enumeration/Active Directory/Kerberoasting]]
    - [[2_Studies/Courses/Current/CERTPREP - PJPT-Practical Jr Pen Tester/Practical Ethical Hacking/2_Enumeration/Post-Compromise Enumeration/Active Directory/Pass the hash|secretsdump.py]]
    - [[2_Studies/Courses/Current/CERTPREP - PJPT-Practical Jr Pen Tester/Practical Ethical Hacking/2_Enumeration/Post-Compromise Enumeration/Active Directory/Pass the hash|Pass the Hash/Password]]
    - Trying to find if there any access Vertically? if not, any access laterally? There might be vertical climbs in other accounts you can pivot to
- No quick wins -> dig deeper:
    - Enumerate with [[2_Studies/Courses/Current/CERTPREP - PJPT-Practical Jr Pen Tester/Practical Ethical Hacking/2_Enumeration/Post-Compromise Enumeration/Active Directory/Bloodhound and Plumhound Enumeration|bloodhound]]
        - Who are the users? 
        - Do we have connection to domain admins?
    - Try to find:
        - Where does the foothold account have access?
            - file shares
            - Accounts in things like servicedesk tix, emails, chats
            - services the user manages
        - Old vulnerabilities, because they do tend to die hard in enterprises
- When in doubt, think outside the box

### Strategy after Owning the Domain
- Provide as much value to client as possible
    - do it again blind to be able to explain it simply, and then if time try again blind to find more vulns
    - Dump the NITDS.dit and crack passwords
    - enumerate shares for sensitive information
- what happens if domain admin access is lost? You need persistence
    - Creating Domain admin account is useful, just be sure to delete it after engagement ends
        - tests for detection
        - gets persistence
    - creating a golden ticket can be useful too

### Example AD attack workflow:
llmnr -> user's hash -> crack hash -> password spray within AD domain -> found new login -> [[2_Studies/Courses/Current/CERTPREP - PJPT-Practical Jr Pen Tester/Practical Ethical Hacking/2_Enumeration/Post-Compromise Enumeration/Active Directory/Pass the hash|secretsdump]] the logins -> local admin hashes found -> respray the AD domain with new accounts found


### Example attack start workflow
1. Enable either:
    1. `responder` (more detailed engagement, see [[2_Studies/Courses/Current/CERTPREP - PJPT-Practical Jr Pen Tester/Practical Ethical Hacking/3_Exploitation/LLMNR Poisoning]] and [[2_Studies/Courses/Current/CERTPREP - PJPT-Practical Jr Pen Tester/Practical Ethical Hacking/3_Exploitation/Active Directory/SMB Relay]] for more)
    2. `mitm6` (quicker engagement, see [[2_Studies/Courses/Current/CERTPREP - PJPT-Practical Jr Pen Tester/Practical Ethical Hacking/3_Exploitation/IPv6 Attacks]] for more)
2. Run scans to generate traffic
3. If scans take too long, look for websites in scope (http_version)
4. Look for default credentials on web logins
    - Printers (google: `passback attacks`)
    - Jenkins
    - webapps
5. If you hit roadblocks, think outside the box
6. If you don't hit something after a significant amount of time, ask client for Initial access to pivot to internal engagement so that you can at least make progress securing that