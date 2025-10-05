
## Quick Use

- *Anonymous connection* - can be used to pivot into privileged connections if available, otherwise can give some information that may be a finding or flag in itself

*Tools*:
- `nmap`, scripts - get SMB version
- `msfconsole` - CLI for Metasploit - can search for `auxiliary/scanner/smb` modules to try and enum version information
- `smbclient` - Attempts a connection to a remote client, can try anonymous connection if told that `Anonymous login successful` by hitting enter when prompted for `WORKGROUP\root's password`
    - `smbclient -L \\\\192.168.57.134\\` - lists files from IPADDR = 192.168.57.134. `\\\\` Are character escapes since we're (often) a Linux machine connecting to a Windows service
    - `smbclient \\\\192.168.57.134\\SHARENAME$` - connects to the samba share found at `SHARENAME$`
## General

**Objectives**: Because `SMB` is used both internally and externally, enumerating any potential exploits on their implementation of `SMB` is a good finding. To find this we need *version information*, which is obtained from `nmap scripts`. We will also need *Metasploit* to do the actual enumeration