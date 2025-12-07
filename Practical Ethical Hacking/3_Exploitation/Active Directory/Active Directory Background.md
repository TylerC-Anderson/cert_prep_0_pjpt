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

## Components


**Logical**:
- *Objects*: managed atomic units of the AD DS
    - `User` - Enables network resource access for a user
    - `InetOrgPerson` - Like a user account, but is used for compatibility with other directory services
    - `Contacts` - Primarily to assign email addresses to external users, and does not enable network access
    - `Groups` - simplifies the admin of access control by assigning permissions policies to all users in that group
    - `Computers` - Enables auth and auditing of computer access to resources
    - `Printers` - Simplifies the process of locating and connecting to printers
    - `Shared Folders` - Enables users to search for shared folders based on properties
- *AD DS Schema*: Defines objects of all types that can be stored in the directory, and enforces rules on object creation and configuration. The *Schema* object types are:
    - `Class` - What objects can be created in the directory, e.g. a User or a device like a compute or printer
    - `Attribute` - Information that can be attached to an object, e.g. a Display name
- *Organizational Units (OUs)*: Active Directory containers which contain users, groups, computers, and other OUs. OUs are used to:
    - Represent the org hierarchically and logically
    - Manage a collection of objects in a consisten way
    - delegate permissions to administer groups of objects
    - apply policies
- *Trusts*: Provide a mechanism for users to gain access to resources in another domain. All domains in a *forest* trust all other domains in the same *forest*, and trusts can extend outside the *forest*. Types include:
    - `Directional` - trust flow is from trusting domain to trusted domain
    - `Transitive` - Trust relationship is extended beyond a 2-domain trust to include other trusted domains
- *Domains*: Groups of objects in an organization, usually grouped by the reachable address (e.g. `Contoso.com` would be a domain). *Domains* have Boundaries that include:
    - An administrative boundary for applying policies to groups of objects
    - A replication boundary for replicating data between domain controllers
    - An authentication and authorization boundary so as to limit the scope of resource access
- *Trees*: Domain trees are a hierarchy of the available domains in AD DS. E.g. `contoso.com` could also have `na.contoso.com`, `emea.contoso.com`, and others. All domains in the tree:
    - Share contiguous namespace with the parent
    - can have additional child domains
    - By default, create a 2-way transitive trust with other domains
- *Forests*: A collection of one or more trees. Forests:
    - Share a common schema
    - Share a common config partition
    - share a common global catalog to enable searching
    - enable trusts between all domains in the forest
    - Share the enterprise admins and Schema Admins groups

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

## Glossary