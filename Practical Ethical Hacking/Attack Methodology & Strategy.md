
1. Enable either:
    1. `responder` (more detailed engagement, see [[LLMNR Poisoning]] and [[SMB Relay]] for more)
    2. `mitm6` (quicker engagement, see [[IPv6 Attacks]] for more)
2. Run scans to generate traffic
3. If scans take too long, look for websites in scope (http_version)
4. Look for default credentials on web logins
    - Printers (google: `passback attacks`)
    - Jenkins
    - webapps
5. If you hit roadblocks, think outside the box
6. If you don't hit something after a significant amount of time, ask client for Initial access to pivot to internal engagement so that you can at least make progress securing that