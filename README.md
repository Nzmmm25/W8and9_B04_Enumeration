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
Findings: 

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
## Challenge 
## Challenge 
## Challenge 
## Challenge 
## Challenge 

# Section C - Advanced Enumeration <br>
## Challenge 
## Challenge 
## Challenge 
## Challenge 
## Challenge 
