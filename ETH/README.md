# Scanning and Enumeration
- ARP host discover in the same subnet -> Arp-scan, **NMAP**, CAIN(windows only)
- ICMP remote host/router -> ping, **NMAP**, Hping3, superscan
- TCP/UDP host discovery -> **NMAP**, superscan, Hping3   
So basically just use **nmap**.
> nmap -sL 100.100.1.1-254 # search host alive *nmbotto de casino fa eh rumore*.  
> nmap -sS -sV <ip> # find services and version   
nmap not only send an ICMP ECHO REQUEST it also perform an ARP ping. (attenction to IPS).

- Banner grabbing 

## DNS enumeration
> snlookup, ls -d, <domain name>; or dig  
+ norecurse, examines only the local dns data. ANSWER: 0 <- EXAMPLE. If i run again the command ANSWER:1. So we can see if someone has queried the site. And we also know the ip af a domain.

# APT (Advanced Persisten Threats)
Example 
- Operation Aurora
- Anonymous
- RBN

Uses of advanced methods (0day, custom exploit) to attack. Are persistent because the attack stop after long time and threats because are organized, motivated etc.
Non APT attacks are against targets of opportunity. APT -> Long-term goals, used to steal a large amount of data in a long time. APT goal **Obtain and mantain access to information**. APT attack don't destroy system or interrupt normal operation. Techniques: dropper delivery services, SQL injection to add malware to wesites, infected USB stik "**drops**" infected HW or SW, social engineering, impersonating users. Hide file (linux file name '.. '), RAM drives "sudo mount -t ramfs -o size=512M ramfs /tmp/ram/", to see ram drives use "df -a".

## APT methods of attack
1. Spear-phishing email
2. User click link; open an hidden application and redirect hydden data
3. Hidden address in a Dropsite, detect browser vulnerabilities, and drop a trojan downloader.
4. Downloader sends a base64-encoded instruction to a different dropsite, which install a trojan backdor
1. Backdoor installed  in \windows\system32  and register in NETSVCS
1. Trojan backdors names slightly different from windows filenames
1. Uses SSL communication with C&C server
1. Attacker interact  cia cutouts with trojan with SSL-encrypted traffic
1. Attacker list computername and User accounts; user pass the hash get local and active direcory acount information
1. Service privilege escalation to netw recoinassance
1. Offline password hash cracking
1. lateral movements by using RDP, SC.exe (to create service) or NET commands(to connect to shares)
1. install additional backdoors Trojans, and ingress point
1. Stolen files are pakaged in ZIP files or RAR pakages, renamed as GIFs

## Detecting APT
- audit changes to filesistem
- SMS alert on administrative logind
- Firewall that montor inbound RDP/VNC/CMD.EXE
- AV, HIPS, filesystem integrity checking
- NIDS, NIPS
- Security informations, Event managements (SIEM)

















# Eth Windows
## Reasons for windows Securoty problems
First of all: Backward Compatibility. Is very important at buisnesses, enabled by default and causes many security problems. Than we have a proliferation of features and so we have an average of 70 MS securoity bllettin per year.


## unauthenticated attacks (Vectors for hack windows) 
- Authentication spoofing (password)
- Network services
- Client software Vulnerabilities
- Device driver  
**Protect these areas**

### Authentication spoofing attacks
Traditional services to attack are: SMB (Server Message Block 445/tcp,139/tcp), MSRPC (Microsoft Remote Procedure Call 135/tcp), Terminal services 3389/tcp, SQL 1443/tcp,1434/udp, share point and web services 80,443.  
**how**  
- Bruteforce-> we all know... (**HYDRA**). Countermeasures: firewall, disable SMB, enforce password, account lockout threshold, enable **audit** account logon failures, **Use all of them**.
- Sniffing on network password exchange. You can sniff LM hash (lan manager easy to crack), Kerberosh pkt. All with **CAIN**.

**Three authentication protocols:**
- LM (lan manager with hash **BRUTTO**)
- NTLM (with encryotion)
- Kerberos (provate and optional publick key encryption)  
### **Attack tools -> CAINKermSniff etc...**: sniffing, bruteforce, dictionary. Attack MITM.

### Kerberos
Send a preauthentication packet which contains a timestamps encrypted with a key derived from the user's password. An offline attack can on that exchange can reveal a weak password 

### authentication sniffing countermeasures
Disable LM authentication. NT LAN manager (NTLM) hashes are harder to crack. Pick good passwords. Use public key encryption. Use built-in windows IPsec to authenticate and encrypt password.

### MITM Countermeasure
If attacker alredy in your lan, very hard. Use authenticated and encrypted protocols,  enforce them with group policy and firewall rules, verify identity os remote server with strong authentication or trusted third parties.

### Pass-the-hash
1. Compromise a machine
1. Dump password hashes stored in **RAM**
1.Use them as credentials for network services whitout cracking them.  
This allow to **compromise a network** after compromising a single machine. **Administrator** loged in the machine before also taken.  
**HOW?**  
**Windows Credential Editor** (WCE)-> dump in-memory username, domain, LM & NTLM hashes. Is a single esecutable (wce.exe) very easy. Support Windows: XP, 2003, vista, 7, 2008. Password are encrypted but **keys are stored in RAM**. Dump existing Kerberos RAM tickets and reuse them. WCE can reply and re-use tickets, but you must compromise a host first.

### Pass-the-hash countermeasures
NTLM vulnerable by design; no countermeasures. **Prevent intrusion first**, if possible two factor authentication.

### Remote unauthencicated expoit (METASPLOIT)
... no comment ...
### Metasploit countermeasures 
Apply patch quickly, audit, log and monitor traffic.

### Device driver exploit.
METASPLOIT but more fun (hight prov mode by driver).

## Authenticated attacks
Privilege escalation (SISTEM is MORE powerful os ADMIN account). Preventing Privilege escalation Keep Win machines patched. **Extracting and cracking passwords** after obltained win machines -> penetrate deeper into the network-> paswordssss (post exploitation disable firewall).

### Grabbing Password Hashesssssss
Local user in windows security accounts manager (SAM) under NT4 and earlier. SAM == SHADOW files for windows.  
Stored in %systemroot%\system32\config\SAM but **it's locked as long as the OS is running**. It's also in the Registry key HKEY_LOCAL_MACHINE\SAM. How to get hashes? Easy, use **CAIN** (cracker tab-> right click-> add to list). **Cain inject a DLL (shared libraries) into a hight privilege process in a running system. Other way to get the hashes, boot target system to an alternate OS and copy SAM file. 

### Cracking hash
NTLM hard to break but earlier version (XP an before) still use LM (wheak). **Windows doesn't salt its hash!** pffff. SO speed up password cracking with raimbow tables

### Backdoors

> psexec \\10.1.1.1 -u Administrator -password -s cmd.exe  
> or  
> nc -l -e cmd.exe -p 8080 #ez rekt  
> telnet <ip> 8080 # attacker  
 
 ### hide file
 - attrib +h filename (set hidden bit)
 - alterbate data stream (ADS)
 > echo 'robe' > out.txt  
 > echo 'segreto' > out.txt:hidden.txt

 same size.  
 Best way to hide file -> Rootkits

## windows system compromised, how to recover? guidelines.
**You shuld just reinstall it completely**  
If you want to clean it, cover four areas: Files, Registry keys, Processes and Network ports.
### Search for suspicius files
known dangerous filename like nc.exe, run antivirus, use tripwire or other tools that tools that identify
changes to system files. 
### Suspicius registry entries
Look for regisry keys that start known backdoors like:
 - HKEY_USERS\.DEFAULT\Software\ORL\WINVNC3
 - HKEY_LOCAL_MACHINE\SOFTWARE\Net Solutions\NetBus Server
**use reg delete to remove**
**Backdor favourite autostart (extensibility points (ASEPs))**
 - **HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Run**
### Suspicius Processes
Malicius porocess whit CPU utilization, end process or kill to stop. Check scheduler queue: shctasks, task sheduler.
### Suspicius Ports
Use **netstat -aon** to vew network connections.
### Windows security features
Windows firewall, automated updates


