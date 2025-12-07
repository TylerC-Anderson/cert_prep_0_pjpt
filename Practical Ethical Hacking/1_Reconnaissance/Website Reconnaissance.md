
## Quick-use
### *Tools*

- `subenum` - CLI tool, replaced `sublist3r` since it stopped being maintained. Installable with APT on Kali
- `Wappalyzer` - Browser Extension - best because it allows you to see the tech on each page as you're navigating it, also not TOO much information such that it's overwhelming
- `builtwith.com` - Web App - Good if you need more detail, but it's a MASSIVE amount of detail, perhaps overwhelming. Good for specific subdomains tho
- `whatweb` - Ruby-based CLI tool - Good for additional detail on a specific target or list of target, instead of the deluge of subdomains `builtwith` provides. HOWEVER, some browsers may block whatweb requests with `Akamai-Global-Host` policies
    - `Akamai` is pretty robust, but some other `CDN/WAF` providers might be pokeable by emulating browser headers, session cookies, and `user-agent` declarations. I used the following, but was unsuccessful getting through `example.com`
        *mise en place*:
        - gather session cookies by navigating to `example.com` and using the firefox `Cookie Editor` extension to export to `Netscape` format. paste this into a cookiejar.txt file
        - collect `user agent` declaration
        - find a couple of choice headers to include
    - resulting command was:

```zsh
 whatweb -cookie-jar=cookiejar.txt -H=Referer:https://shop.example.com/ -H=sec-fetch-site:cross-site -U='[REDACTED MY USERAGENT DATA]' https://www.example.com                                            
https://www.example.com [403 Forbidden] [REDACTED CLIENT DATA], Country[UNITED STATES][US], HTTPServer[AkamaiGHost], IP[[REDACTED CLIENT DATA]], Strict-Transport-Security[max-age=15768000], Title[Access Denied], UncommonHeaders[x-reference-error,x-ak-cache,permissions-policy]
```


## General

**Objective:** 
- Gather email subdomains belonging to the target, especially potentially ones that may be sensitive to exploitation, such as `dev.example.com` or `test.example.com`. You generally want to find domains that are in scope, but that they shouldn't want the user to have access to.
- Gather technological information on the target's web applications and site. Information such as the version numbers and languages involved may reveal potential weaknesses.

Some examples of good subdomains:
- `admin.example.com`  
- `www.example.com` 
- `api.example.com`  
- `dev.example.com`  
- `staging.example.com`  
- `test.example.com`  
- `vpn.example.com`  
- `git.example.com`  
- `mail.example.com`  
- `docs.example.com`  
- `cdn.example.com`  
- `login.example.com`

