# PND's notes 2020
## Layering concepts

communication between host  -> organized in task -> each assigned to a __layer__.

Each layer
- offers a service to layer __above__
- exploits the services of layer __below__

The task involves the excange of messages that follow a set of rules know as __protocol__.

## Encapsulation Decapsulation

### Layered Architectures
- ISO/OSI (7 layers)
- TCI/IP (4 layers)

common idea: __packet switched network__

Each layer has a type of address:
- application layer : www.cybersecurity.uniroma1.it
- transport layer: (port number), [0..65535]
- internet layer: IP addressthat identify a nic
- datalink layer: MAC address also identify a nic

### ports

source -> __randomly chosen by os!!__
destination -> __determines the required service__

### TCP / UDP
|connection | connectionless|
|-|-|
|syn->||
|<-syn+ack|data->|
|ack->||

### Services tcp/upd
|TCP|UDP|
|----|----|
|Ftp|Dns|
|Ssh|Dhcp|
|Telnet|Tftp|
|Smtp|Snmp|
|Http|Rip|
|Imap||
|Ssl||

# IPV6 Addressing

Benefits of ipv6
- Larger address space
- Stateless autocunfiguration
- End-to-end reachability whitout nat 
- Better monìbility support
- Peer-to-peer networking  (easier for VoIP or QoS)

# IPv6 Address notation

2001:0DB8:AAAA:1111:0000:0000:0000:0100

2001 -> 16 bits (hexadecimal)

Rules for compressing
- Omitting Leading 0s
- Double colums :: (all 0s inside)

## IPv6 address type

- Unicast (2000::/3) -> (3FFF::)
- Link-Local (FF80::/10) -> (FEBF::)
- Loopback ::1/128
- Multicast Assigned (FF00::/8) 

## Multicast info (FF00::/8)
|1111 1111| [Flag (4 bits)]  [Scope (4 bits)] |...|||||||

Scope is  a 4-bits field used to define the range of the multicast packet.
- 1 Interface-Local Scope
- 2 Link-Local scope
- 5 Site-Local scope
- 8 Organization-Local scope
- E Global Scope

Flag  
- 0 - Permanent, well-known multicast address assigned by IANA.
- Includes both assigned and solicited-node multicast addresses.
- 1 - Non-permanently-assigned, “dynamically" assigned multicast address.
- An example might be FF18::CAFE:1234, used for a multicast application
with organizational scope.

__IPv6 does not have broadcast address__

### Global Unicast Address

3-1-4 Rule

Global Routing Prefix - Subnet prefix - Interface Id 

## SLAAC

Stateless Address Auto Configuration

ICMPv6 Discover Protocol

# TODO 

# IPv6 vs IPv4

IPv6 takes advantage of 64-bit CPUs.
Simpler IPv6 Header.
Fixed 40 byte IPv6 header.

IPv4 contains IHL (internet header leght) IPv6 have fixed header (40 bite) lenght so there isn't in his header.
And some little optimization.

IPv6 Next Header. In this place IPv4 had a Protocol parameter. For both protocols, the field indicate the type of the following header.
Common value:
- 6  = TCP
- 17 = UDP
- 58 = ICMPv6

IPv6 doesn't have checksum header, and doesn't have the padding because is fixed at 40 byte.

options added after the first header  setting the next header to indicate the presence of an extension header with some more option and info. So the options are computed only by the destination instead hop by hop  as in IPv4.

# Link-Local attacks
- network sniffing
- ARP poisoning
- IPv6 Neghbor Dyscovery threats
- IPv6 Rogue RA (RA spoofing)
- Rogue DHCP

## Network sniffing

Sniffer must be along the path or al least in the same network.
- non switched lan (LAN with HUB)
- lan with switches. Breaking switch segmentation (MAC FLOODING)
- possible man in the middle

## ARP spoofing
we all know what is it. **POSSIBLE MITM**

## IPv6 Neighbor Discovery
similar to arp onli in IPv6. **POSSIBLE MITM**

## ICMPv6 redirect
similar to ICMPv4. Inform  an originating host of the IP address of an router that is on the **local-link** but is closer to the destination. **POSSIBLE MITM**

# EXAMPLES
## Rogue RA

When an IPv6 enabled system recives a router advertisement, if SLAAC is enabled, it will be parto of another network and **will receive a new route**, optionally a default gateway...

In the and, with control of DNS and IPv6, the attacker can:
- sniff all client traffic;
- attempt MITM attack;
- Impersonate servers/systems and capture user credentials;
- gain access into the other networks of the system;

## DHCP rogue server (DHCO starvation)

- Anyplave where macof works, you can dos a network by requesting all the avaiable DHCP adresses;
- Once all the addressses ar gone the attacker can now provide a rogue DHCP server to provide ip to clients;
- Since DHCP tresponse include DNS servers and default gateway entries, the **attacker can pretend to be anyone**;
- All the MITM are now possible;

# Proxies

## Benefits of forwarding proxy
- Authentication, Authorization, Auditing, whitelisting, blacklistings...
- Caching
    - Problems:
        - how long is it possible to to keep a document in cache and still be shure that is up-to-date ?
        - how to decide if the document are worth caching and for how long ?
    - Solutions:
        - HEADS http request (very inefficent);
        - *if-modified-since* request header;

forwarding other non-HTTP request ?
- HTTP tunneling;
- HTTP CONNECT;
- The proxy enstablishes the TCP connection and becomes the middle point; 

### HTTP CCONNECT
allow to use of any protocol that use TCP. IDEA: The proxy simply recives the destination host the clients want to connect to and enstablishes a connection on its behalf. Then, when the connection is enstablished, the proxy server continues to proxy the TCP stream **unmodified** to and from the client. Clearly the proxy can perform Authentication before accepting to foreward the data stream.

Proxy can be used to filter content (COntent-filtering Proxy). After user authentication HTTP proxy control the content that can may be relayed (virus malware, facebook...).

## Reverse proxy
- Forward proxy operates on behalf on the client;
- Reverse proxy operates on behalf on the server;

- Typical functions:
    - Load balancing;
    - Chace static content;
    - Compresson;
    - Accessing several servers into the same URL space;
    - Securing of the internal servers;
    - Application level controls;
    - TLS acceleration;

### Internal server protection
The reverse proxy receives the requests form the clients and then issues new, prim and proper requests to the real server. No direct connection with the outside also means defense against DOS. Can support HTTPS with server that only support HTTP. Can add AAA to services that not have them (Example an IoT device behind a firewall that must be accessible form the outside).

# Firewalllzzzzz

- Stateless firewall: Bad idea. Very complex to write rules.
- Stateful firewall: Can keep tracks of enstablished connections.

## Iptables
Command to see the rules:
>.  
> iptables -L \
> iptables -L -n -v --line-numbers  
> .

Iptables chain:
 - **FORWARD**
 - **INPUT**
 - **OPUTPUT**

 To remove all rules:
 > iptables -F 

 Useful Commands:
 iptables switches|Description
 ---|---
 -t tables|Specifies the table (filter if not given)
 -j target|Jump to the target (it can be another chain)
 -A chain |Append rule to a specific chain
 -F |Flush a chain
 -P policy | CHange the default policy (ACCEPT \| DROP \| REJECT)
 -p protocol|Match the protocol type (TCP \| UDP \| ICMP ...)
 -s ip-address|Match the source ip
 -d ip-address|Match the destination ip
 -p tcp --sport port|Match the tcp source port (also work for UDP)
 -p tcp --dport port|Match the tcp destination port (also work for udp)
 -i interface-name|**Match** input interface (from which the packet enters)
 -o interface-name|**Match** output interface (from which the paket exits)
--icmp-type type|Match specific icmp packets type
-m module|Uses an extenzsion module
-m state --state s|Enable connection tracking. (NEW \| ESTABLISHED \| RELATED \| INVALID)
-m multiport|Enable specification of several port eith one single rule
-m mac --mac-source|Match packets with a specific source MAC address



*RELATED: The packet is the starting of a related transaction like FTP*


 ### Examples stateless rules
 > iptables -A input  -p icmp -icmp-type echo-request -j DROP  
 > iptables -A input -p tcp --destination-port 80 -j ACCEPT  
 >   
 >Allow both 80 and 443 for the webserver on inside.
 > iptables -A forward -s 0/0 -i eth0 -d 192.168.1.58 -o eth1 -p TCP --sport 1024:65535 -m multiport --dport 80,443 -j ACCEPT  
 >  
 >The return traffic from webserver is allowed, but only if session are opened:  
 >iptables -A forward -s 0/0 -i eth0 -d 192.168.1.58 -o eth1 -p TCP -m state --state ESTABLESHED -j ACCEPTED

 # NAT (Network address translation)
## NAT command
### DNAT
iptables -t nat -A PREROUTING -p tcp -d 80.182.53.192 -dport 80 -j DNAT -to-destination 10.0.0.2:80  
iptables -t nat -A PREROUTING -p tcp -d 80.182.53.192 -dport 110 -j DNAT -to-destination 10.0.0.3:110  
iptables -t filter -P FORWARD ACCEPT  
echo 1 > /proc/sys/net/ipv4/ip_forward  
#### la sintassi  DNAT
iptables -t nat -A POSTROUTING -o (interfaccia sulla extranet) -s (intranet/mask) --source-address

### MASQUERADE 
iptables -t nat -A POSTROUTING -o ppp0 -j MASQUERADE    
iptables -t filter -P FORWARD ACCEPT  
echo 1 > /proc/sys/net/ipv4/ip_forward  

### sourceNAT
#### la sitassi SNAT
iptables -t nat -A POSTROUTING -p (protocol {tcp | udp })  
- -s (source address)
- –sport <source porta>
- -d (destination)
- –dport <dest port>
- -j SNAT
- –to-source (source address modificato)
> iptables -t nat -A POSTROUTING -o ppp0 -j SNAT -to-source 150.92.48.25  
    
### port redirect
> iptables -t nat -A PREROUTING -d 192.168.1.1 destination-port 80 -j REDIRECT -to-port 10000  

## Routed vs Transaparent firewall modes.  
In **routed** mode, a firewall is a hop in the routing process. It **is** a router responsible of is own internal networks.  
In **transparent** mode, a firewall works with data at Layer 2 (Data link layer (MAC)) and is not seen as a router hop to connected devices (it can be implemented by bridged NICs).

## Routable IP Addressing
- Routable adresses need to be unique on the internet to be **PUBLICLY** rechable -> **public IP adresses**
- Non-routable adresses -> **private IP addresses**
    - **RFC 1819**
        - 10.0.0.0 - 10.255.255.255 (10/8 prefix)
        - 172.16.0.0 - 172.31.255.255 (172.16/12 prefix)
        - 192.168.0.0 - 192.168.255.255 (192.168/16 prefix)
    - Loopback addresses
        - 127.0.0.0 - 1727.255.255.255 (127/8 prefix)
    - Shared address space
        - 100.64.0.0 - 100.127.255.255 (100.64/10 prefix)
## Network Address Translation (NAT)
Translate the address (f.e.: between incompatible IP addressing). NAT **is a routed firewall**: it can filter requests from hosts on WAN side to host on LAN side, allows host requests from the LAN site to reach the WAN side. But does not expose LAN hosts to external  port scans.  
## NAT goals
1. Provate network uses just one IP address provided by ISP to connect to the Internet.
1. Can change  address of devices in private network without notifying outside world.
1. Can change ISP without change the address of the devices on the internal network.
1. Devices on the internal network are not visible  by outside world (a security plus).

### Source NAS (SNAT)
Translate **outgoing** IP source addresses on the packet header from the internal host to the wan ip addresses. The session is **masqueraded** as coming from the NATting device. The **NAT table** is where associations between requests ans internal IP addresses are kept.

Types of NAT:
- Basic (internal ip -> multiple external ip) not common due to shortage of IPv4 eddresses.
- NAPT Network address port translation (internal ip -> single ip external port).

NAPT router block by default all incoming ports by default (many applyvation have had problems with NAPT in the past).

### Destination NAT (DNAT)
Enables servers located **inside** the firewall/router LAN to be accessed by clients located outside. The service appears to be hosted by the firewall/router. It also called **port forwarding** or **virtual server**.

## NAT with iptables
four built-in-tables
1. MANGLE -> manipulate bit in TCP header ( not for nat not for packet filter ).
1. FILTER -> packet filtering ( used for filtering packets ).
1. NAT -> network address translation ( used for nat (DNAT, SNAT, MASQUERADE, REDIRECT) ).
1. RAW -> exceptions to connections tracking.

# VPN Virtual Private Network
Security goal of a vpn
- Traditional
    - Confidentiality of data
    - Integrity of data
    - Peer Authentication
- Extended
    - Replay protection
    - Access control
    - Traffic analysis protection

Usability goals: **Trasparency** VPN shuld be invisible to users, software, hardware. **Flexibility** VPN can be used between users, applications, host, sites. **Simplicity** VPN can be actually used.

- Site-to-site security.
- Host-to.site security.
- Host-to-host security.

It looks best to introduce security in the:
- Transport layer.
- Network layer.

bacause:
implemented at->|pysical layer|datalink|network|transport|application
---|---|---|---|---|---
Confidentiality             |on cable                       |on link(virtual cable) |between hosts/sites|between apps/hosts/sites|between users/apps
Integrity                   |on cable                       |on link                |between hosts/sites|between apps/hosts/sites|between users/apps
Authentication              |none                           |none                   |for host or site|for user, host, sites|user
Reply connection            |none                           |none                   |between hosts/sites|between apps/hosts/sites|between apps
Traffic analisys protection |on cable                       |on link                |host/site info exposed|protocol/host/site info exposed|all but data exposed
Access control              |physical access                |physical access        |to host/site|user/host/site|only data access secured
Trasparency                 |full transparecy               |full                   |user and SW possible|user and SW possible|only user trasparency
Flexibility                 |hard to add new sites          |hard to add new sites  |may need SW or HW mod|HW or SW mod|SW mod
Symplicity                  |excellent                      |excellent              |**OK** site to site **NO OK** host to site|**OK** site to site **NO OK** host to site|depends on application

So best VPN on **Transport** or **Network** layer.
## SSL tunneling

Tunneling  is an operation of a network connection on top of another network connection. It allow two hosts or sites to communicate trough another network that they do not want to use directly. The site so site tunneling **enables a PDU to be trasported from one site to another whitout its content being processed by host on the route**. Idea: encapsulate the whole PDU in another PDU sent out on the network connecting twho sites.

Instead a **secure tunneling** enables a PDU to be transported from one site to another whitout its content beeing seen or chenged by hosts on the route. Idea: Encrypt the PDU and then encapsulate it in another PDU sent out on the network connecting the two sites.

Tunneling offers the basic level to provide a VPN.

## Secure Socket Layer
SSL3.0 has become TLS standard (RFC2246) with small changes. Applies security on the Transport layer. If implemented on boundary routers or proxy can provide a tunnel between two sites (Tipically LAN). Placed on top of TCP so no need to change TCP/IP stack or OS. **It provide a secure channel (byte stream)** and optional server authentication whit pubblik key certificates.

Certificates func:
1. Google richiede un certificato a Comodo
1. Comodo verifica che è davvero Google che sta effettuando la richiesta.
1. Google invia a Comodo la propria chiave pubblica.
1. Comodo utilizza la propria chiave privata per firmare digitalmente la chiave pubblica di Google.
1. Comodo dà a Google la chiave pubblica firmata, questa rappresenta ora il certificato SSL/TLS di Google.
1. Ti colleghi col tuo browser al sito web di Google.
1. Google invia al browser il certificato SSL/TLS.
1. Utilizzando la chiave pubblica di Comodo, che si trova nell'archivio dei certificati radice del tuo browser, viene verificata la firma di Comodo sul certificato SSL/TLS di Google.
1. Viene generata una chiave segreta e si utilizza la chiave pubblica di Google, ovvero il certificato, per crittografarla.
1. Viene inviata la chiave segreta crittografata a Google.
1. Google decrittografa la chiave segreta con la propria chiave privata e la trattiene.
1. Il tuo browser e Google utilizzano la chiave segreta condivisa per crittografare le comunicazioni.

# IPsec
Is a network layer protocol suite for providing security over ip. Is part of IPv6, and an addon for IPv4. It can handle all tree possible security architectures:
**Feature**|**Gateway to Gateway**|**Host to Gateway**|**Host to Host**
---|:---:|:---:|:---:
Protection between client and local gateway| NO | N/A Client is VPN endpoint | N/A Client is VPN endpoint
Protection between VPN endpoints           | YES | YES | YES
Protection between remote gateway and remote server (behind gateway) | NO | NO | N/A Client is VPN endpoint
Trasparenct to users | YES | NO | NO
Trasparency to user's systems | YES | NO | NO
Trasparency to servers | YES | YES | NO

## IPsec services
Basic functions, provided by separate (sub-)protocols:
- **Authentication Header (AH)**: Support for data integrity and authentication of IP packets
- **Encapsulated Security Payload (ESP)**: Support for encryption and (optionally) authentication.
- **Internet Key Exchange (IKE)**: Support for key managenent etc.  

**Service**|**AH**|**ESP(encrypt only)**|**ESP(encrypt+authentication)**
---|:---:|:---:|:---:
Access Control| **+**|**+**|**+**
Connectionless Integrity|**+**||**+**
Protection between VPN enpoints|**+**||**+**
Data origin authentication|**+**||**+**
Reject replayed packets||**+**|**+**
Payload confidentiality||**+**|**+**
Metadata confidentiality||partial|partial
Traffic flow confidentiality||(\*)|(\*)

**approfondisci autenticazione con IPsec**

IPsec vs TLS: TLS is much more flexible because is in the upper levels. IPsec has to run in kernel space. IPsec much more complex and complicated to manage with.
But crypto is insufficient for web security. Trust: what dpes the server really know about the applications ? Unless client-side certificates are used, absolutely nothing. SSL provide a secure pipe. **Someone** is at the other end, but you don't know who. Usually there isn't user authentication in SSL, but in the application layer. **SSL the client knowledge of the server** the client receives the server's certificate, but a certificates means that someone has attested to the **binding of some name to a public key**. Who has done the certification ? Is it the right name ? **Every browser has a list of built-in CA** Hundreds of certificate authorities. Do you trust them all to be honest and competent? Do you even know them all ? **IT'S ALL MATTER OF TRUST**. So the cryptography itself seems correct, the human factors are dubious. Most user don't know what certificate is, or how to verify one.   

## Fundamentals of tunneling
- tun -> encapsulate IP layer
- tap -> encapsulate Ethernet layer
><br>
> // useful commands <br><br>
> check our local interfaces<br>    
> <b>ip link</b> <br><br> 
> //create a new tun interface<br>
> <b>ip tunnel add tun0 mode ipip remote \<ipaddressR\> local \<ipaddressL\></b> <br> <br>
> // activate tun interface<br>
> <b>ip addr tun0 up 10.0.0.1/30 mtu 1500</b><br><br>
> Openvpn static key: generate key on one side<br>
> <b>openvpn --genkey --secret secret.key</b><br><br>

# Intrusion Detection System (IDS) 
An intrusion detection system aims (obiettivo) at detecting the presence of intruders before serius damage is done. Second generation of IDS are IPS (Intrusion Prevention System), also produce **responses** to suspicius activity, for example, by modifying firewalls rules or blocking switches ports. 
- IDS -> **report** intrusion so is out of band -> **passive**
- IPS -> **Block** intrusion so is in line -> **active**  
List of activities monitored by IDS/IPS:
- Atented and succesful breach
    - recoinassance
    - pattern of specific commands in application session (login should contain auth command)
    - Content types with different field of application protocols (buff ove)
    - Network packet pattern between protected servers and outside world
    - Priviledge escalation
- Attacks by legitimate users/insiders
    - illegitimate use of root privileges
    - Unauthorized access to resources and data
    - Command and program execution
- Malware
    - Rootkits/Trojans/Spyware
    - Viruses, zombie and worms
    - Scripts
    - Polimorphic and Metamorphic viruses
- DOS attacks

## Types of IDS
- **HIDS -> Host based IDS**
    - monitor events in a single host to detect suspicius activity
    - Typically deployed on critical host offering public services
    - Advantage -> better visibility into behaviour of individual applications running on the host
- **NIDS -> Network based IDS**
    - Analyses network, transport and application protocol activity
    - Often placed behind a router or firewall that is the entrance of a critycal asset
    - Advantage -> single NIDS/IPS can protect many host and detect global patterns
- **WIDS -> Wireless IDS**
    - Analyses wireless network protocol activity (not T or A layers)
    - Tipically deploied on or near an organization's wireless network

## HIDS
Only monitors traffic on one specific system (no promiscuos mode). It looks for unusual events or patterns that may indicate problems, Ex software changes, changes configurations, unexpected activity, unauthorized access and activity etc.
## NIDS
Usually operated in a promiscuos mode -> sniffers. Can have multiple NICs to monitor multiple network segments. Areusually connected to switches whit ports mirrored (or in SPAN mode, **S**witch **P**ort **AN**alizer) all the traffic generated whitin ALL the ports of the switches are replicated on the mirrored port where the NIDS is placed. Often has different sensors plaved in different network (Distributed detection system).

## Other tipes of IDS
- File integrity checkers (ex Tripwire) -> monitor changes to key system config files.
- Flow based IDS (ex NetFlow) -> tracks network connections, enstablish patterns of normal traffic.
    - Alert when unusual traffic is generated
    - Can give a good overall situational view on large network.
- Hybrid detection capabilities
    - Augmet or replace signature-base detection
    - Usually anomaly/behavior based detection (Pseudo-artificial intelligence)
    - Often require a trainig periods to establish a baseline

# How to recognize an intrusion 
Behavior-based (anomaly detection) or Signature detection (misuse detection). **Learn and classify anomalies**, Behavior is tipically described in terms of set of features. The feature set shuld describe all relevant aspectsof the behavior to be recognized. Anomaly detection require some form of learning, usually based on data mining in actual osservations. **(A too large feature set means that both traning and classification will take unecessary long time )**.

# Signature based IDS principles
Basucally is a packet sniffer on steroids. Signature -> payloads of packets, timing, etc. **SNORT** is one of the best known intrusion detectors. Easy to learn easy t use, rules stored in **/etc/snort/rules** directory. It can't inspect encrypted raffic (SSL/VPNs), not all attacks arrive from the network, record and process huge amount of traffic.

# Honeypot
Is a security resouce whose value lies in it being attacked, probed or compromised. A **honeypot** is usually a single computer, a **honeynet** is a network of computers usually protected by a firewall to regulate traffic. IDEA attract the attackers.

# Example of IDS rule evasion
We want to detect"*USER root*" in packet stream. Scanning for it in every packet is not enough, becouse attacker can split attack string into several packets; this will defeat stateless NIDS. Even recording prevous packet's text is not enough, the attacker can send packets out of order and even full reassembly  of TCP state is not enough, because the attacker can manipulate checksums, TTL, fragmentation, so that certain packets are seen by IDS but dropped by receivind application.

# Signature vs behavior
- Signature -> còlearly indicates the detected attack method.
- Behavioral-based alerts -> indicate: attack type, rule violated, statistical profile violated.
Behavioral protection **cannot** identify the specific attack  or exploit that was blocked. So **COMBINE**.

Best solution is to combine both signature and behavioral rules.
- no false positives
- no false negatives

# OBSERVATION
Legitimate traffic in real networks contains anomalies. Protocol anomalies come from custom applications that use off-the-shelf protocol libraries, but use them in unexpected ways. Behavioral anomalies come from exceptional, but often critical, buisness processes.
  

