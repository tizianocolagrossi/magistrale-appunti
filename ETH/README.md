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
**WPA** no major weaknesses until 2017, however, if you use a weak pre shared key, it can be found with a dictionay attack. But PSK is hashed 4096 times, can be up 63 characters long, and include the SSID.
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

## The Administrator account of a Windows server has been compromised. Host software cannot be re-installed for business reasons. With these assumptions, how do you plan and implement post-exploit activities for the host recovery. In particular, list the areas of the system on which to intervene, to restore the host’s security. Discuss in detail at least one of these areas of intervention, listing the activities to be carried out, the tools, the line commands to be used, etc.
If you want to clean it without reinstall all you must cover four areas: files, registry keys, processes, network ports. Search in the system for some dangerous filename such as 'nc.exe', run antivirus, use some tools that identify some changes to the filesystem and system files. Look for registry keys that lauch some backdoors (WINVNC3 and NetBus server) and delete them using reg delete. Search for extensibility points (ASEPs)**HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Run**. Then searh for process with CPU utilization, kill them to stop, check scheduler queue (shctasks and task sheduler). For look the port that are listening launch **netstat -aon** . Finally check for firewall and autoupdate.

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
