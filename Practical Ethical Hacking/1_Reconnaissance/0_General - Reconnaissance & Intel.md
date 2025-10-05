## General

**Objectives**:

Use OSINT and passive reconnaissance to find initial footholds and potential attack vectors based on scope of target.

*Key intelligence to gather include*:
- **Externally Exposed Asset Inventory** — Owned domains, subdomains, IPs, ASNs, cloud tenants, Internet-facing services, web apps/APIs, browser certs, DNS posture.
- **Access Surface** — Key employees, Employee identifier patterns, business emails, auth endpoints, email infrastructure.
- **Identity, Secrets, Supply Chain** — public profiles, Public code, CI/CD artifacts, third-party vendor access, SaaS integrations, leaked credentials.
- **Misconfigurations & Quick Win Paths** — Dangling cloud resources, exposed admin consoles, takeover candidates, weak auth, low-effort entry.


**Overview**:

Generally defined as **Passive reconnaissance**, it breaks down into *2 main categories*


### Category 1: **Web/Host** -> ~={red}THE FOCUS OF TCM-SEC PRACTICAL ETHICAL HACKING COURSE=~ 

- ~={green}FIRST STEP ALWAYS=~: **Scope, [[Rules of Engagement]], and Target Validation**- Esp not if performing a bug bounty such as on `bugcrowd`
    - domain/IPs allowed
    - assets out-of-scope
    - allowed techniques
    - reporting requirements
    - SLAs
    - point of contact.
- **Finding Subdomains**
    - *Tools*:
        - `Google`
        - `dig`
        - `nmap`
        - `Sublist3r`
        - `Bluto`
        - `crt.sh`
- **Finding Machines**
    - *Tools*:
        - `arp-scan -l` - list all mac addresses on this network
- **Fingerprinting**
    - Services they're running on a website or host
    - Are they running a web server? What kind- IAS, apache?
    - What ports are open? What versions of those ports such as FTP versions
    - Play around *A BIT* with the website, see how it functions. What's the flow? Are there any weird logins you maybe shouldn't have access to? Does anything look glitchy or broken? What's the typical user story, and what might they NOT be expecting?
    - *Tools*:
        - `nmap`
        - `Wappalyzer`
        - `WhatWeb`
        - `BuiltWith`
        - `Netcat`
- **Data Breaches**
    - Past data leaks that have credentials dumped which have *STILL* not been changed.
    - Are there any passwords or logins available? Any users that tend to use weak passwords?
    - *Tools*:
        - `HaveIBeenPwned`
        - `Breach-Parse`
        - `WeLeakInfo`



### Category 2: **Physical, Social, OSINT, and Social Engineering**
- Location Information
    - Satellite images
    - Drone recon
    - Building Layout
        - Badge readers
        - break areas
        - Smoking locations
        - security personnel: posts, routes, rotations
        - fences
        - doors - are they broken or left open?
    - How tight or lax is their security? Do they pay much attention to badges?
- Personal Data and Job information
    - Employees
        - Name
        - Job role/title
        - Phone number
        - email
        - Direct Report - key personnel you might need to know the name of for a cover story
    - Pictures
        - Badge - can it be emulated?
        - desk
        - Computer screenshots