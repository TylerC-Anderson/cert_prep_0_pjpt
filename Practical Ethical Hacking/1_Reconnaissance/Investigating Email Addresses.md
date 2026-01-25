###### *See also*: [[2_Studies/Courses/Current/CERTPREP - PJPT-Practical Jr Pen Tester/Practical Ethical Hacking/2_Enumeration/Investigating Breached Credentials]]

### Key Personnel to enum

**Objective:** discover employee email patterns and high-value contacts (CISO, IT leads, sales, HR) and validate them with passive methods.

Generally, you want information on how they structure their organization and what format their email addresses are in, e.g. `f.lname@company.com` or perhaps `flastname@company.com`

Combining both of the above means that you can find insecure passwords or logins which would allow an attacker unauthed access. You can also abuse `Forgot my password` tooling to expose whether their real email is something other than the alias you found, which may give more organizational intelligence as well

**Key targets**

- Executive security: CISO, Head of Security, SOC Lead
- IT: IT Manager, Infrastructure, DevOps
- Business: Sales, HR (recruiters useful for social engineering)
- Role accounts: `security@,` `infosec@`, `admin@`, `support@`

### Methodolgy

1. Passive OSINT (preferred): corporate site, `LinkedIn`, `GitHub`, PDFs, press releases, cert transparency (`crt.sh`), `urlscan`, `Wayback`.
2. Chaining: `theHarvester` for bulk collection, then manual confirmation on high-value hits.
3. Validation (non-invasive): Email verification APIs (`Hunter`, `EmailHippo`). `DNS` `MX` check.
4. Confidence scoring: 0–100 based on corroboration and source quality.

**Output**  
CSV: `email, name, role, pattern, sources, confidence, notes`
### *Tools*

**PREFERRED** - `hunter.io` browser extension, really good for emails, and `theHarvester` is second for sheer breadth of information gathered quickly, speed, and cost (completely free for many sources)

- `hunter.io` - Web App - really cool, but paid with only 50 free creds per month. Also has a browser extension
- `theHarvester` - CLI tool - Comes with Kali, annoying to install on non-Ubuntu systems otherwise, but really cool because you can chain multiple lookups
    - *Notes*: run the low-noise sources first (`crtsh`, `gitlab`, `wayback`, `urlscan`) before noisy lookups. Parse `-f` output for unique emails and dedupe.
    - Good uses:
- ```shell
theHarvester -d example.com -b crtsh,certspotter,gitlab,urlscan,commoncrawl,waybackarchive,rapiddns,robtex,subdomaincenter,subdomainfinderc99,hackertarget,duckduckgo,yahoo,otx,threatminer,threatcrowd -l 1000 -f example_free_osint
```
    - This example does the following (if done manually, do it in roughly this order)
        - `crtsh,certspotter` — grab certificates and subdomains.
        - `gitlab,urlscan,commoncrawl,waybackarchive` — hunt for emails in repos, snapshots, and page captures.
        - `rapiddns,robtex,subdomaincenter,subdomainfinderc99,hackertarget` — expand hosts and DNS history.
        - `duckduckgo,yahoo,leakix,otx,threatminer,threatcrowd,virustotal` — scrape for stray contact pages, PDFs, and paste/leak traces.
- `emailHIPPO` - web app, free Email Address verifier
- `Ghunt` - Could be good, 20 Euro minimum to try it out tho, and only 30 searches per month
- `Phonebook.cz` - Web App - Everything's redacted, and now it's p shitty imo

### *Safety & Noise*

- Avoid `SMTP`/`VRFY` unless permitted. Document every source link for each discovered address.