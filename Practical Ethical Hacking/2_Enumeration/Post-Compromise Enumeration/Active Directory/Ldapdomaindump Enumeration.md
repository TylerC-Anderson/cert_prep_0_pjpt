
## Quick-Use

**Commands**:
- without compromised user in the `htb.local` active directory: `ldapsearch -x -H ldap://TARGIPADDR -b "DC=htb,DC=local"`
- To gather information using compromised user: `sudo ldapdomaindump ldaps://TARGIPADDR -u 'ADNAME\username' -p Password1 -o output-dirname`
- Can gather usernames in the `htb.local` AD with: `windapsearch.py -d htb.local --dc-ip TARGIPADDR -U`
    - this version is anonymous, but you could probably also provide a username

*Tools*:
- 

## General

**Objectives**:


**Overview**:
Gathers information on domain users, policies, trusts, AD computers, and groups
## Glossary