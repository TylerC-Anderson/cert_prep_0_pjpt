
**Objective**: Gather data on Open/Vulnerable ports with a spoofed 3-way Handshake (`SYN`, `SYNACK`, `ACK` --> Mnemonic: *Sin-snack-ack!*)

**General Functionality**: Sends `SYN` packets and waits for a `SYNACK` packet, which signals that the port is open. If the scanner receives an `RST` packet instead, that port is known to be closed and we can move on.

**Common Uses**:
- *Stealth Scanning* - `nmap -sS IPADDR` - Used to be undetectable, but is NOW VERY DETECTABLE... assuming they know what they're doing.
    - ***~={orange}WARNING=~***: ~20% of SOCs successfully picked up NMAP recon according to Hacking Mentor circa 2019, however this may be changed. Keep this in mind if you don't want to be seen
    - *Mechanism of Action*: Sends `SYN`, receives `SYNACK`, but then instead sends `RST` flag to attempt to keep the connection attempt hidden
        - This is why it's detectable now. SOCs could be tracking RST flags being sent from an IP and then block that IP because you're likely a malicious actor if you're pounding their ports with RST flags over any/all ports. This would just be part of many heuristics that they use, which would include connection fingerprinting (looking at odd patterns in SYN rates or Timing), IP rep, and anomalous TCP options. So yeah, not so stealthy anymore and likely less so than Cyber Mentor suggests.
- *The CTF Special* - `nmap -t4 -p- -A IPADDR` - "At a scale from 1-5 (slow to fast), scan at a 4 (`T4`), scan all ports (`-p-`), and enable OS detection, version detection, default NSE script scanning, **and** traceroute (i.e. broad service/OS/version + script info) (`-A`)"
    - ***~={orange}WARNING=~***: Loud and proud, not stealthy at all
    - Useful for CTFS because stealth usu. doesn't matter in those. Good also for common pentesting on a target (such as for compliance-oriented pentesting contracts), since stealth usually isn't required. Be advised that it’s noisy and may violate stealthy/authorized-scan constraints, if any.
- *Get thee to a UDP* - `nmap -sU -T4 IPADDR` - Good if you need UDP (obv) but terrible if you're on a time constraint. This scan takes a really long time because UDP is a connectionless protocol, which means the response time can be much longer