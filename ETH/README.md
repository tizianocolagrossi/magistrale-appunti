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

# Remote connectivity and VoIP
- Dial-up hack
- Voicemail Hacking
- Virtual private network haking
- Coice over IP attacks
mani companies still use dial-up connections (connecting to old server). Dial up hacking is similar to other hacking (footprint, scan, enumerate, exploit), automated by tools: wardialer or demo dialer. Phone number footprinting: identify blocks of phone to load into a wardialer.  
**Bruteforce Voicemail Hacking**, is similar to dial-up hacking metods. Required components. Tools Voicemail box hacker 3.0, VrACK 0.51. **Countermeasures** deploy a lockout on failed attempts; log observe voicemail connections.  
### VPN hacking
VPN has replaced dial-up as the remote access mechanism. **GOOGLE HACKING** Using filetipe:pcf to find profile setting files for Cisco VPN client -> download and import -> connect and attack. Password stored in PCF file can be used for password reuse attack (tools cain etc). IPsec -> 1 sniff IKE (tools -> **IKEprobe**), 2 initiate connection. **Countermeasures**: discontinue IKE Aggressive mode use; use token-based authentication scheme.
### SIP scanning
The transport of voice on top of an ip network (Signaling protocols: H.323 and SIP), SIP scanning: discover SIP proxies and other devices tools **SiVuS, SIPVicious**. Countermeasures: network segmentation between VoIP network and user access segment. 

# Wireless hacking

### Case of study in Wireless Hacking WEP
We have a store with the point-of-sale system connectet trought a WEP encryption wifi system (Wired Equivalent Privacy). A hacker at the parking near the store turn on the laptop with a directional antenna and the NIC in promicuous mode. **Airckrack-ng** suite of tool for hack wifi networks.
- **airodump-ng** -> to sniff 802.11 frames including WEP inizialization vectors (IVs), loks for SSID of interest and its MAC address.
- **aireplay-ng** -> to spoof as a client co capture ARP and reply it to collect enough IVs.
- **aircrack-ng** -> to krack WEP key prom the capture file. Disable promiscuous mode and connect to the network of the store.  
#### Background on IEEE 802.11 wireless lan
ISM(Industrial, Scentific, Medical) unlicensed bands. 2.4v GHz->802.11 b/g/n channels 1-14 (non overlapping channels 1,6,11), 5Ghz->802.11 a/n channels 36-165 (all non overlapping).
#### Session establishment
Most common mode just uses an access point. Ad Hoc devices connected peer-to-peer (like ethernet crossover cable). **PROBES** Client send a probe request for the SSID it is looking for, it repeat this request for each channel for a response from the SSID (**probe response**). Afterthe response client sends **authentication request**. If system use open authentication the AP accept any connection. The alternate system, **shared-key authentication**, is almost never used (used only in WEP). The WPA security mechanism have no effect on authentication, they take effect later. **ASSOCIATION** client send **association request** and AP sends an **associationresponse**.
#### Security mechanism
**MAC filtering** or **Hidden network** (omit SSID from beacons). CLient can send a **broadcast probe requests** and do not specify the SSID (APs can be configured to ignore them)
## WPA vs. WPA2
802.11i specifies encryption standards.
- WPA implements only part of 802.11i -> TKIP (Temporal Key Integrity Protocol)
- WPA2 instead impelemts both -> TKIP and AES (Advanced encryption standards)

#### WPA personal VS. WPA enterprise
On **WPA personal** we have one password for all the users. Password storen in the wireless client and if the password is changed on AP we must change manually form all the devices connected at the AP and **Wireless access cannot be individually managed**. Instead in **WPA enterprise** when users try to connect to Wi-Fi, they need to present their enterprise login credentials. Users never deal with the actual encrytpion keys. Attacker cannot get the network key from clients and offers **individual control** over the connection.

#### WPA-PSK VS. 802.1x
**WPA-PSK** uses pre-shared key. **WPA-Enterprise 802.1x** uses 802.1x and a RADIUS server, EAP (Extesible Authentication Protocol), which bau be one of: EAP-TTLS, PEAP, EAP-FAST. Both WPA-PSK and WPA-Enterprise use Four way handshake to establish: Pairwise transient key (used for unicast communications), Group tempral key (used for multicast and broadcat communications). Three Encryption Options: **WEP** -> use RC4 and easily exploitable. **TKIP** -> quick replacement for WEP (runs on old hardware), still use RC4 but no major vulnerabilities are known. **AES-CCMP** -> (Advanced Encryption Standard with Ciper Block Chainig Message Authentication Protocol) most secure reccomended.

## **Equipment for attack**
- NIC that support promicuous mode -> (Alfa AWUS050NH, Ralink RT2270F chipset)
- Windows (easy find driver, bad hack tools) or **Linux** (hard to get drivers but hacking tools are much better) **KALI** or **PARROT**
- Antennas
    - Omnidirectional
    - Directional
- And more...
#### Identifying Wireless Network Defenses
- SSID can be forund from mainly these packets (Beacons, Probe request, Probe responses, Association and Reassociation Requests)
    - IF SSID broadcasting is off, just deauthenticate all to force reassociation.
- MAC address control
    - Sniff for users search for the mac of one user and spoof
    - >macchanger -m xx:xx:xx:xx:xx:xx [interface]

#### Attacks
To **crack WEP** you need to capture enough data -> 60000 IVs to crack 104-bit key.  
**WPA** no major weaknesses until 2017, however, if you use a weak pre shared key, it can be found whit a dictionay attack. But PSK is hashed 4096 times, can be up 63 characters long, and include the SSID.
#### Password brute force
**WPA-PSK** PSK shared among all the user of a wireless network. Four way handshake between client and AP: using PSK and SSID to derive encryption keys.
 - PSK, 8-64 char, hashed 4096 timeswith SSID (trilions of guesses)  
Capture four-way handshake to crack PSK offline ( wait or deauth clients of ). **Brute forcing**-> aircrack-ng | coWPAtty (use SSID specific raimbowtables) | pyrit offload hashing to GPU whit multiple cores.

#### LEAP 
Leap is a proprietary protocol from Cisco Systems developed in 2000 to address the security weakness common in WEP. Is an 102.1x schema using a RADIUS server.
**Is fundamentally weak because it provides ZERO RESISTANCE to offline  dictionary attack**. It solely rely on MS-CHAPv2 to protect the user credential usedfor wireless LAN authentications. **But MS-CHAPv2 is notoriously weak** because it does not use salt in NT hashes, use a weak 2 byte DES key and sends usernames in clear text. Because of this offline dictionary and bruteforce attack can be made much more efficient.

#### Authentiation attacks WPA enterprise
- Identifying 802.1x EAP types
    - Capture EAP handshake
    - Wireshark shows EAP type
    - Unencrypted username in RADIUS server EAP handshake
- LEAP (lightweight EAP)
    - asleep -> offline brute-force attack eith LEAP handshake and wordlist
- EAP-TTLS and PEAP
    - a TLS tunnel between an unauthenticated client and a RADIUS server (AP relays and has no visibility)
    - less secure inner authentication protocol (often in clear text)
    - AP impersonation and man-in-the-middle attack
    - **hostapd** turn your NIC into an AP
    - **asleep** offline bruteforcein inner authentication protocol 
    - Countermeasure (check the box to validate server certificate on all clients)

## Summary wireless hacking
- WEP
    - Passive attacks and ARP reply with fake with fake authentication 
    - cracked in 5 min
    - **DO NOT USE IT NEVER**
- WPA-PSK
    - could be brute-forced, trough high complexity 
    - one PSK fits all -> put other user at risk
- WPA enterprise
    - LEAP
        - could be brute-forced, needs extremely complex passwords
        - **DONT USE IT** 
    - EAP-TTLS and PEAP
        - relatively secure with multilayered encryption
        - subject to AP impersonations and MITM attacks
        - **always have client check server chertificate**








