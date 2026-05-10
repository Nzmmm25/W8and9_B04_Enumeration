# Enumeration <br>

IP Address: <br> 
Attacker (Linux)           = 192.168.56.101 <br>
Victim (Metasploitable2)   = 192.168.56.102

# Section A - Basic Enumeration <br>
## Challenge 2 - Fast Nmap Scan
Commands Used: **nmap -F 192.168.56.102**
<img width="541" height="463" alt="image" src="https://github.com/user-attachments/assets/0d853f0b-63a8-4aab-bba1-5cd0ac302f03" /> <br> 
Findings: A lot of open ports and different services.

## Challenge 5 - TTL OS Fingerprinting
Commands Used: **ping 192.168.56.162**
<img width="526" height="245" alt="image" src="https://github.com/user-attachments/assets/75cdd2b0-b321-433b-b4b1-1a6fa7b35e5f" /> <br>
Findings: By reading the TTL value, we can guess the Operating System the victim is using. <br>
TTL 64 = Linux/Unix <br>
TTL 128 = Windows <br>
TTLS 255 = Cisco / BSD  

## Challenge 7 - SMTP VRFY / EXPN
Commands Used: <br>
**nc 192.168.56.102 25** <br> 
**VRFY root** <br>
**EXPN admin** <br>
<img width="443" height="126" alt="image" src="https://github.com/user-attachments/assets/62fde9b2-12b4-49d2-8938-e4b91e5b32e3" /> <br>
Findings: <br>
| Command | Result |
| :--- | :--- |
| `nc 192.168.56.102 25` | 220 metasploitable.localdomain ESMTP Postfix (Ubuntu) |
| | ESMTP Postfix mail server - Software versions|
| `VRFY root` | 252 2.0.0 root |
| | 252 means the server cannot verify the user |
| `EXPN admin` | 502 5.5.2 Error: command not recognized |
| | 502 error indicates that the command is either disabled or not implemented |

## Challenge 9 - FTP Banner
Commands Used: **nc 192.168.56.102 21**
<img width="208" height="56" alt="image" src="https://github.com/user-attachments/assets/59333cdc-d3e1-48ba-9a6f-65d846e697be" /> <br>
Findings: This command utilizes netcat to check the version of an open port 21 (FTP) <br>
Version found was = vsFTPd 2.3.4

This version of vsftpd has been reported to be vulnerable to backdoor attacks. 

## Challenge 10 - Anonymous FTP Login
Commands Used: **ftp 192.168.56.102**
<img width="335" height="192" alt="image" src="https://github.com/user-attachments/assets/94dd8a58-b3e9-497e-afcc-abf340ce41fd" /> <br>
Findings: This command utilizes the open port (21) for FTP Access. <br>
Anonymous login successful by entering username as "anonymous" and leaving the password empty. <br> 

Next, other commands can be ran to utilize this login access. Commands like: <br> 
**ls -la** = Can see the directory listing. <br>
**PWD** = To see what directory we are in. <br>
**cd (directory)** = to access other directories.
<img width="489" height="216" alt="image" src="https://github.com/user-attachments/assets/efc2d42a-eadb-4990-87d9-d204246d8a6c" /> <br> <br>

# Section B - Intermediate Enumeration <br>
## Challenge 11 - SMB NSE Enumeration
Commands Used: 
**nmap --script smb-os-discovery -p445 192.168.56.102**
<img width="540" height="334" alt="image" src="https://github.com/user-attachments/assets/9f830667-cb7f-4ce8-b7d9-ae628d90486b" />

Findings: Victim's Operating System information <br>

| Property | Discovery Result |
| :--- | :--- |
| **OS** | Unix (Samba 3.0.20-Debian) |
| **Computer Name** | metasploitable |
| **NetBIOS Name** | metasploitable |
| **Domain Name** | localdomain |
| **FQDN** | metasploitable.localdomain |
| **System Time** | 2026-05-10T04:10:22-04:00 |

<br>

**nmap --script smb-enum-users -p445 192.168.56.102**

<img width="481" height="200" alt="image" src="https://github.com/user-attachments/assets/206d43aa-6f53-4599-9a64-930ad0f27297" /> <br>
<img width="481" height="186" alt="image" src="https://github.com/user-attachments/assets/372742bd-24ea-4864-92d2-202d464d0bcd" />

Findings: A lot of disabled user accounts. However, 2 enabled users. Attackers can use this information to easily target users in the domain.

| RID | Full Name | Flags |
| :--- | :--- | :--- |
| 3000 | msfadmin | Normal user account |
| 3002 | just a user,111,, | Normal user account |

<br>

## Challenge 12 - Enum4Linux
Commands Used: **enum4linux -a 192.168.56.102**
<img width="858" height="494" alt="image" src="https://github.com/user-attachments/assets/c9778d3c-12ff-4fc2-81ac-5ffe9b279687" />

In "Share Enumeration", we can see Sharenames and Workgroup. Important thing to highlight are;

**1) Sharename = IPC$ and ADMIN$** <br>
**2) //192.168.56.102.tmp  Mapping: OK Listing: OK Writing N/A**
   - "Mapping and Listing = OK" means attacker can connnect to this share without credentials to view files in it.

<br> 
<img width="857" height="674" alt="image" src="https://github.com/user-attachments/assets/10af7dd1-db71-435f-840a-a8452e31d31a" />

In the same command, this is for "Password Policy Information"

Findings: 

| Findings | Meaning | 
| :--- | :--- |
|Account Lockout Threshold: None | Attackers have unlimited tries to guess the password without a lockout. Enables bruce force attacks |
|Minimum Password Length: 5 | Minimum length of a password is set to 5 characters. Easier to guess |
|Password Complexity: Disabled | The password is not forced to have special characters, uppercase and lowercase or numbers |

## Challenge 13 - NFS Exports
Commands Used: **showmount -e 192.168.56.102** 
<img width="275" height="85" alt="image" src="https://github.com/user-attachments/assets/cfb11e22-3bde-4227-a556-51d62ba3fba0" />

Findings: "/*" indicates a full directory access for the attacker. 


## Challenge 16 - Version Detection
Commands Used: **nmap -sV 192.168.56.102**

<img width="924" height="596" alt="image" src="https://github.com/user-attachments/assets/d8e886ea-e9ee-4210-8603-c9800c3d802e" />

Findings: Open ports and their service versions. Some may be a vulnerable version. 

| Port | Service | Version | Vulnerability |
| :--- | :--- | :--- | :--- |
| **21** | FTP | vsftpd 2.3.4 | This version contains a malicious backdoor triggered by a `:)` in the username, leading to a root shell. |
| **22** | SSH | OpenSSH 4.7p1 | Susceptible to username discovery and potential exploitation using outdated Debian-specific weak SSL keys. |
| **25** | SMTP | Postfix smtpd | Allows unauthenticated attackers to verify valid system usernames using the `VRFY` and `EXPN` commands. |
| **139/445** | SMB | Samba 3.X - 4.X | Samba 3.0.20 is vulnerable to CVE-2007-2447, allowing instant root access via a shell metacharacter in the username. |

## Challenge 17 - OS Detection
Command Used: **nmap -O 192.168.56.102**

<img width="525" height="111" alt="image" src="https://github.com/user-attachments/assets/0b5d02d3-3ca1-4cf6-a30b-34b524440af6" />

Findings: 
- Victim's Operating System is Linux version 2.6.X (2.6.9-2.6.33)
- "Network Distance: 1 Hop" is due to VirtualBox Environment, therefore no hops to routers or firewalls. 

# Section C - Advanced Enumeration <br>
## Challenge 
## Challenge 
## Challenge 


