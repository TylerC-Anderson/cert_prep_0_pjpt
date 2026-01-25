#### IP Addresses - Layer 3, the Network Layer

IP is communicated over **layer 3**

IPv4 is most common, but IPv6 was created in case we ran out of IPv4 addresses (we did, but still don't use IPv4 anyway...)
- $2^{32}$ possible addresses for IPv4 (about 4 billion)
- ~$3.403 *10^{38}$ possible addresses for IPv6, but few use IPv6 addresses.

`ifconfig`- prints network information

![[2_Studies/Courses/Current/CERTPREP - PJPT-Practical Jr Pen Tester/Practical Ethical Hacking/z_attachments/Pasted image 20251005004018.png]]

![[2_Studies/Courses/Current/CERTPREP - PJPT-Practical Jr Pen Tester/Practical Ethical Hacking/z_attachments/Pasted image 20251005004105.png]]

~={green}1=~ - IPv4 address
~={red}2=~ - IPv6 address

We ran out of IPv4 address space long ago, so why don't we use IPv6?
- A: We don't need to, because we use **NAT**: Network Address Translation, and can assign Private IP addresses using **NAT**
- Below is an image showing the common IP address spaces, categorized by a *Class*
![[2_Studies/Courses/Current/CERTPREP - PJPT-Practical Jr Pen Tester/Practical Ethical Hacking/z_attachments/Pasted image 20251005004127.png]]

- **Class C** is the most common Home and Small business usage
    - Less hosts per network, but **many** networks
    - 192.168.x.x is almost **GUARANATEED** to be a private home address or home business
- **Class A** are usually Enterprise-level networks
    - Less networks possible, but **many** hosts
- **Class B** falls somewhere in-between the above 2
- Not pictured, there are also **Class D** and **Class E** ranges:
    - **Class D**- Used for Multicasting (single host sending a single stream of data to many peers accross the Internet), such as streaming servers.
    - **Class E**- Reserved for research and experimental purposes
- Outside of the above ranges

This class-based system has been superseded by the **CIDR** (*Classless Inter-Domain Routing*) system. Under this system, the ISPs own specific IP address ranges, and distribute single addresses in those ranges to their customers for a rental fee.

Here's the ranges Comcast (my ISP) rents out:

| *Netblock (CIDR)* | *Number of IP Addresses* |
| ----------------- | ------------------------ |
| `73.0.0.0/8`      | 16,777,216               |
| `50.128.0.0/9`    | 8,388,608                |
| `98.192.0.0/10`   | 4,194,304                |
| `68.32.0.0/11`    | 2,097,152                |
| `67.160.0.0/11`   | 2,097,152                |
| `69.136.0.0/11`   | 2,097,152                |
| `76.96.0.0/11`    | 2,097,152                |
| `174.48.0.0/12`   | 1,048,576                |
| `24.0.0.0/12`     | 1,048,576                |
| `69.240.0.0/12`   | 1,048,576                |
| `71.192.0.0/12`   | 1,048,576                |

#### MAC Addresses - Layer 2, the Data Link Layer

Physical address of the device, such as the network interface card (**NIC**). Typically a 6 octet hex such as: 
`00-0c-29-94-55-05`

may also be represented as:
``00:0c:29:94:55:05``

`ifconfig` will also print the MAC address, under the `ether` field
![[2_Studies/Courses/Current/CERTPREP - PJPT-Practical Jr Pen Tester/Practical Ethical Hacking/z_attachments/Pasted image 20251005004147.png]]



#### TCP, UDP, and the Three-way Handshake - Layer 4, the Transport Layer

- **TCP**: *Transport Control Protocol*, Connection oriented protocol
    - Most commonly used protocol
    - Mechanism: **The 3-way Handshake**:
        - **SYN Packet**- Initial call (like a "*Hello!*")
        - **SYN/ACK Packet**- Call back to notify of receipt and readiness to continue (like a "*Hello to you too! I've got some time to talk.*")
        - **ACK Packet** (*Acknowledgement*)- Finalizes and establishes the connection (like a "*Okay! Let's talk.*")

- **UDP**: *User Datagram Protocol*, connectionless protocol

We will often scan *both* protocol types as a penetration tester

**Ports**- Object that is opened on a connection to communicate using certain protocols. There are over *65,000* of such ports

#### Common Ports and Protocols

| *TCP*          | *UDP*        |
| -------------- | ------------ |
| FTP(21)        | DNS(53)      |
| SSH(22)        | DHCP(67, 68) |
| Telnet(23)     | TFTP(69)     |
| SMTP(25)       | SNMP(161)    |
| DNS(53)        |              |
| HTTP(80)       |              |
| HTTPS(443)     |              |
| POP3(110)      |              |
| SMB(139 + 445) |              |
| IMAP(143)      |              |
- *FTP(21)*- File transfer protocol
- *Telnet(23)*- Log into machine remotely, **INSECURE!**
- *SSH(22)*- Log into machine remotely (encrypted version of telnet)
- *SMTP(25), POP3(110), IMAP(143)*- Email protocols
- *DNS(53)*- Domain Name Service, relates a string (name) to the IP address that routes to the service being requested that matches that string
- *HTTP(80) / HTTPS(443)*- HyperText Transfer Protocol, connects to services on the open web. The *S* stands for *Secure*, as *HTTPS* is encrypted during transfer (can't read the packets, only sniff them)
- *SMB(139 + 445)*- SMB file shares, mostly used for Windows. Also called *Samba*. Famous exploits of SMB include the *WannaCry(MS17-010)* virus. AKA EternalBlue because it was built from that
- *DHCP(67, 68)*- Domain Host Configuration Protocol, is used to assign an IP address to a Host upon connection to a network or after expiry. May be either Static (doesn't change) or Dynamic (changes after IP address lease expiry). Static Address are typically reserved by the MAC address
- *TFTP(69)*- Trivial File Transfer Protocol, uses UDP instead of TCP, but is otherwise similar to FTP in function
- *SNMP(161)*- Simple Network Management Protocol, used occassionally. Good for recon when encountered, can surface `"community" or "public" strings`


`wireshark`- Tool to examine packets being communicated over the transport layer, including ports and different protocols

#### The OSI Model

Model of the Networking Stack and its devices.

1. **P**lease  - **Physical**     - data, cables, cat6
2. **D**o      - **Data**         - Switching, MAC Addresses
3. **N**ot     - **Network**      - IP Addresses, routing
4. **T**hrow   - **Transport**    - TCP/UDP
5. **S**ausage - **Session**      - session management
6. **P**izza   - **Presentation** - WMV, JPEG, MP4
7. **A**way!   - **Application**  - HTTP, SMTP

#### Subnetting

**Subnets**- the setting of a **sub**-**network** by reserving the highest value bits of the IP Address to serve as the address where that network can be reached, and every bit that isn't reserved is able to be modified, which would represent a specific host's address within the **subnet**.

It's easiest to think of this system as functioning where each highest value `1` bit in sequence in the `subnet` locks the bit determining the IP address in place. See the diagram below.

![[2_Studies/Courses/Current/CERTPREP - PJPT-Practical Jr Pen Tester/Practical Ethical Hacking/z_attachments/Pasted image 20251005004158.png]]

So, **subnets** in essence restrict the number of hosts that can be found at the *network ID*. The *network ID* is always going to be the lowest value address possible within the **subnet**. Meanwhile, the *broadcast IP* is always going to be the highest value address in that **subnet**

**Subnets** can be expressed as a *Netmask*, which is the decimal number of the binary sum of the unbroken sequence of highest value bits in the last octal that are reserved (locked in, to use the terminology from the diagram above) instead of an indication of the exact bit count being reserved

The *Netmask* of the above would be ~={blue}255.255.255.192=~
**Classless Inter-Domain Routing (CIDR) Notation**- this is writing the IP address with the count of the bits that are locked in appended to the IP address with a `/`, such as `192.168.2.64/26` in the example above. The number following the slash (`/`) is also called the prefix length (so the prefix length is `n` for `x.x.x.x/n`)

*Netmask* is also in the output of `ifconfig` (`ipconfig` in Windows). ~={red}See below(1)=~. The equivalent of the *Netmask* below would be a `/24` network, so the IP address in that notation would be `192.168.57.139/24`. This is also called a **wack-24** network.

![[2_Studies/Courses/Current/CERTPREP - PJPT-Practical Jr Pen Tester/Practical Ethical Hacking/z_attachments/Pasted image 20251005004208.png]]

**Network Hosts** can be easily calculated from netmask or **CIDR** notation by writing out the binary notation of the Netmask if in Netmask form, or `n`=prefix length number of 1's where, counting the 0's following the unbroken sequence of `1` bits in the binary notation (we'll call this value that results from this process $x$), and retrieving $2^{x}$, subtracting two hosts since the Network ID address and the broadcast IP address are reserved, and that final value is the **host count**.
- *Quick in your head calculation*- Memorize that `/24` is 256 total hosts, then if the CIDR number is less than `24` you multiply 256 by 2 for each integer below `24`, or multiply for each integer above `24` and **BOOM! You got the hosts after subtracting 2 from that final value**. Can basically do this with your fingers