
## Quick Use

*Tools*:
- `nikto` - `nikto -h http://192.168.109.129` -  Enums web servers, listing vulnerabilities if in the DB that nikto accesses.
- `Firefox about:config` - If you find an outdated config and you need to enable legacy compatibility
- `ffuf` - directory fuzzer capable of recursion (see commands for usage)
- `gobuster` & `dirbuster` & `dirb` - Directory Busting tools
    - IP address format to use in dirbuster (summoned with `dirbuster&`):
        - http://192.168.109.129:80/
    - Select a wordlist, found in the `/usr/share/wordlists/dirbuster/` directory
    - File extension needs to be appropriate for the webservice you're busting (e.g. `php` for *Apache webservers*, or `aspx` and etc for *Microsoft webservers*)
        - Can also add other filetypes you wanna look for, such as the following, BUT NOTE that each additional file extension will require another pass through the wordlist, meaning it's a linear time efficiency hit:
            - `txt`
            - `zip`
            - `rar`
            - `pdf`
            - `docx`
- `Browser Dev Tools` - View Page Source Code for code or code comments with information disclosures, passwords, keys, user accounts, or (if in CTFs) hints or flags
- `BurpSuite` - Use repeater to change API calls

**Commands**:
- `gobuster dir -u http://TARGETIPADDR[:PORTNUM]/ -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt` - Single directory buster (non-recursive, see `ffuf`)
- `ffuf -u http://TARGET/FUZZ -w /usr/share/seclists/Discovery/Web-Content/common.txt -recursion -recursion-depth 2 -e .php,.html -mc 200,301,302 -o ffuf_results.json` - FUZZ is the injection point - basic recursive fuzzing with depth 2, append .php/.html
`
## General

**Objective**: Find usable exploits for port 80 (HTTP) or 443 (HTTPS), investigate directories by *Directory Busting*, look for hidden; test/example; or misconfigured pages, look for leaked server names.

*~={orange}Possible Findings=~*: 
- Default Web-page - This would signal to an attacker that the target uses poor hygiene (what other pages have they left open by accident? What other subdomains might this page still be linked to? What other things have they left around haphazardly?)
![[Pasted image 20251001214911.png]]


