
## Quick-Use

**Commands**:
- `ssh username@IPADDR -p PORTNUM`
    - `-p PORTNUM` is optional, 22 is default
    - `username@` is optional as well, can sign into default user if set
- IF YOU ENCOUNTER THIS OUTPUT: `no matching key exchange method found. Their offer: BLAH1 BLAH2`
    - try `ssh IPADDR -oKexAlgorithms=+BLAH2` or other values in the list of `BLAH`s to find cyphers
        - *1)* if told `Their offer: ssh-rsa,ssh-dss` do `ssh IPADDR -oKexAlgorithms=+BLAH2 -oHostKeyAlgorithms=+ssh-rsa`, otherwise skip to *2)*
        - *2)* after shown cyphers do: `ssh IPADDR -oKexAlgorithms=+BLAH2 -c blah-cypher` (or `ssh IPADDR -oKexAlgorithms=+BLAH2 -oHostKeyAlgorithms=+ssh-rsa -c blah-cypher` if *1)* was necessary) to try different results for `blah-cypher`
- 

*Tools*:
- 

## General

**Objectives**: Connecting without attempting to actually login via password (i.e. hitting `ctrl-c` when prompted for password) can sometimes give you a banner which can tell you information about the machine or version of `SSH` being ran

**Overview**:

## Glossary












