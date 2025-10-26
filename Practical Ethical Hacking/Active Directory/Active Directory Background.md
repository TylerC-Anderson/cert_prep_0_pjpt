
## Components

**Physical**:
- *Domain Controller*: Server within the AD Directory Server that controls everything, hosting the actual directory. Domain Controllers:
    - Host a copy of the AD DS directory store
    - provide authentication and authorization services
    - replicate updates to other domain controllers in the domain and forest
    - allow admin access to manage user accounts and network resources
- *AD DS Data Store*: Contains the db files and processes that store and manage directory info for users, services, and apps. The AD DS Data store:
    - consists of the *Ntds.dit* (VERY IMPORTANT) file
    - Is stored by default in the `%SystemRoot%\NTDS` folder on all domain controllers
    - Is accessible only through domain controller processes and protocols (think of getters and setters in OOP)

**Logical**:
- 

## General

**Objective**: 
Can be exploited without ever attacking patchable exploits. 
- Features
- trusts
- components
- and more
...are all abusable

**Overview**:
*Active Directory* is the most popular Identity management service (among other less popular options, used by 95% of Fortune 1k companies). It was created by Microsoft to manage Windows domain networks. It does the following:
- Manages user group policies, like password complexity requirements
- Stores information (including addressing data, like a phone book for windows) related to objects such as:
    - Computers
    - Users
    - Printers
- Authenticates using *Kerberos* tickets
    - Non-windows devices like Linux machines, firewalls, etc. can auth to *AD* via *RADIUS* or *LDAP*



## Glossary