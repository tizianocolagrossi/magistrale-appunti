# Terminology
- **Multiplexer**: Allow a single communication channel to carry simultaneous data trasmission from many terminals.
- **Modem**: Changes signal from analog to digital and back to analog.
- **CO**: central office.
- **LEC**: Local excange carrier.
- **OLT**: Optical line terminal
- **ONU**: Optical network unit
- **ONT**: Optical Network Termination (NT: network termination)
- **ODN**: Optical distribution network
- **AON**: Active Optical Network aka P2P
- **PON**: Passive optical netowork, passive branching of fibes via optical splitters and tree-based topologies
- **Fiber to the excange** : The optical fiber terminates to the CO, and the CO is connected with te user via copper based line (e.g. ADSL).
- **Cross-talks**: Wires sharing the same cable interfere one each other
- **Backhaul:** portion of the network comprises the intermediate links between the core network, or backbone network, and the small subnetworks at the edge of the network. The most common network type in which backhaul is implemented is a mobile network. A backhaul of a mobile network, also referred to as mobile-backhaul connects a cell site towards the core network. The two main methods of mobile backhaul implementations are fiber-based backhaul and wireless point-to-point backhaul.
- **Latency** Is the time of the reaction of the network
- **handoff/Handover**: procedure that keep the connection during the mobility of the user. A base station pass the control of the connection to another one base station.
- **crosstalk** signal that pass from a cable to another. And generAte interference.
- **BER** bit error rate.
- **OLT** Optical line terminal.
-  legge di Shannon è: C = B * log2 (1+SNR), better C if better B or less SNR.


# Introduction

## Types of telecommunication Networks
Alredy known **WAN** **MAN** **LAN** And then we have: 
- **Virtual Private Network** (VPN): A secure network that uses the internet as its main backbone network, but relies on firewall and other security feaures. A **VPN Enabling Technology is IPSec** (SVPN). It is an open architecture for IP- packet encryption and authentication, thus it is located in the **network layer**. IPSec adds  additional headers/trailers to an IP packet and can encapdilate (tunnel) IP packets in new ones.

## Telecommunications media
- Twisted-pair wire
- Coaxial cable
    - minimize interference and distorition
    - allow high-speed data trasmission
- Fiber optics
    - Glass fiber that condict pulses of light generated by lasers
    - Size and weight reduction
    - Increased speed and carring capacity
- Terrestrial Microwave
    - Line-of'sight path between relay stations spaced approximately 40 km apart
- Communication satelites
    - Geosynchronous orbits
    - Serve as relay stations for communications signals transmitted from earth stations
- Cellular Systems
    - Each cell is typically from one to several square miles in area
    - Each cell has its own low-power trasmitter radio rela .sd1
    - Computers and other communcations pricessors coordinate and control the trasmission to/from mobile users as they move from one cell to another 

## Network Topologies
vb le sapemo

## Trasmission fundamentals 
pure qua shannon SNR etc, digitale, analogico, FDM, TDM

## Network functional areas
### access network

Is the part of a communications networks which connects subscribers to their immediate service provider. It may be further divided between feeder plant or distribution network, and drop plant or edge network. **The access network domain plays an important role in a network by connecting communications carriers and service providers with the individuals and companies they serve.

### core network 

Usually are structured in a mesh topology. Provides any-to-any connections among devices in the network. Consist of multiple switches or consist of IP routers. **The internet could be considered a giant core network** (it really consist of many isp that run their own core networks, and those core networks are interconnected). Significant to core networks is *"the edge"*, where networks and user exist.

### edge of the network

The edge may perform intelligent functions that are not performed inside the core network. For example if the core network is usig MPLS (Multiprotocol Label Switching), an edge switch may examine packets and select a path through the network based on various properties of the packet. The core network then switches the packet, instead of doing hop-by-hop, and this improve the performance. (in this case the core network is dumb, wile the edge is smart)


## Distribution network

### Fixed line access network
An access network refers to the series of wires, cables, and equipment lying between a cunsomer/buisness thelephone termination point and the local telephone excange. **The local excange contains banks of automated switching equipment to direct a call or connection to the customer**.

The access network is one of the oldest assets a telecom operator owns, ans is constantly evolving, growning as a new customers are connected, and as new services are offered (this make the access network one of the most complex networks in the world to mantain and keep track of). And **it is the most valuable assets an operators owns** since this is what is phisically allow them to offer a service.

### Local loop 
In telephony the local loop aka subscriber line is the physical link circuit that connects from the demarcation point of the customer premises to the edge of the carrier or telecommunicationsservice provider network. At the edge of the carrier in a traditional PSTN (Public Switched Telephone Network) scenario, the local loop terminates in a circuit switch housed in an ILEC Central Office.

Traditionally was wireline fron customer to CO (twisted pair copper) for voice communications. But modern implementation can include a digial loop system in fiber optic cables. **A local loop may be provisioned to support data communication applications, or combined voice and data such as Digital Subscriber Line (DSL).

can be used to carry:
- Analog voice used in traditional **POTS**
- Integrated Service Digital Network (ISDN)
- variant of Digital Subscriber Line (DSL)

Local loop connections include:
- Electric local loop: PLC (power line comm)
- Optical local loop: (Fiber optics services)
- Satellites local loop: (comm satellite and cosmos internet connections)
- Cable optical loop: (Cable modems)
- Wireless local loop (WLL): LMDS (Local Multipoint Distribution Services), GPRS (General Packet Radio Services), HSDPA etc...

### Type of access
#### **Copper cables** 
provides both high-speed broadband and existing phone services. The major advantage of this network is that is wifely avaiable. Existing copper-based network have gradually been expanded dring several decades and their architectures are not optimized with regard to current technologies.

If an entirely new network were to be build today, it would not be based on ue of copper-based techologies. **One problem is that are disigned mainly to carryng POTS** while a growing share of the traffic is based on IP

#### **Optical access**
This domain will be the mode of choice for fixed access in the coming years.

#### **Wireless Access**
This domain enjoys the highest expectation from the standopoint of ubiquitous networking 

# XDSL 
is a family of technologies that provide digital data trasmissions over the wires of a telephone network. DSL stands originally for digital subscriber loop.  

## Asymmetric DSL
Original market driver: distribution of video on demand (VoD) (failed for this target). The distinguishing characteristic of ADSL over other forms of DSL is that the volume of data flow is greater in one direction than the other. Providers usually market ADSL as a service for consumers to connect to the Internet in a relatively
passive mode: able to use the higher speed direction for the "download" from the Internet but not needing to run servers that would require high speed in the other direction.
### Speed
Older ADSL standards can deliver 8 Mbit/s to the customer over about 2 km of unshielded twisted pair copper wire. The latest standard, ADSL2+, can deliver up to
24 Mbit/s, depending on the distance from the central office.

### ADSL frequency bands

ADSL uses two separate frequency bands, referred to as the upstream and downstream bands.
- The upstream band is used for communication from the end user to the telephone central office
- The downstream band is used for communicating from the central office to the end user.
- With standard ADSL, the band from 25.875 kHz to 138 kHz is used for upstream communication, while 138 kHz–1104 kHz is used for downstream communication

### ADSL and cross-talks
Two kinds of cross-talk noise exist: far-end cross-talk (FEXT) and near-end cross-talk (NEXT). Crosstalk typically increases with frequency -> significant impairment for high speed DSL.

#### FEXT
FEXT is the cross-talk between a transmitter and a receiver placed on opposite sides of the cable. FEXT signals travel the entire length of the channel. Since for ADSL “short” cables are used, the signal carried on other pairs, even though coming from far away, are not strongly attenuated and create interferences that affect other pairs. In order to reduce this kind of noise a cable usually doesn't contain more than a dozen twisted pairs.

#### NEXT
NEXT is the cross-talk between a transmitter and a receiver placed on the same side of the cable.
Receiver's signals are softer than transmitter's one, since come from far
away and thus there is a strong interference which reduces quality of useful received data.
NEXT is one of the reason of the frequency division for upstream and downstream in ADSL.

### ADSL: Modulations
- CAP: stands for Carrier-less Amplitude/Phase
modulation, and describes a version of QAM in which
incoming data modulates a single carrier that is then
transmitted down a telephone line. The carrier itself is suppressed before transmission (it contains no information, and can be reconstructed at the receiver), hence the word “carrier-less” (single
carrier but suppressed)
- DMT: (Discrete Multi-Tone)(multicarrier) 
256 sub-bands of 4,3125 kHz each, so
occupying 1.024MHz. Each sub-band is QAM64 modulated for
clean sub-bands, down to QPSK for
noisier lines.

#### **Principle of DMT modulation**
It divide the operational ADSL bandwith into very small subchannells. Discrete carrier or tones are used in the center of each data subchannell. These carriers are used to transmit data indipendently in each subcarrier by means of a specified QAM modulation.

Independent subchannels can be manipulated
individually with consideration of the line conditions, 
If a subchannel is experiencing external interference it
may not be used in favor of other subchannles
**DTM can dynamically adapt the data rate to the line
conditions**
##### **Water filling**
The signal power for each subcarrier is determined as the depth of the
liquid in a pool. Knowing the discreate values of p(fk) for each subcarrier fk one may deduce the number of bit per simbol to associate to the QAM costellation used in each subchannel.

## VDSL Vectoring
Crosstalk cancelling by injecting an “anti-signal”
on each crosstalk-impaired line
- Requires full synchronization over the full vectored system
- All data samples are shared between all the lines
- Requires calculation of the “anti-signals”
- Requires a crosstalk estimating mechanism to derive the crosstalk coefficients

## Point to Point Protocol: summary
PPP was designed for simple links to transport packets between two peers. The ppp encapsulation provides for multiplexing of different network layer protocols simultaneously over the same link. It provides a Link Control Protocol (LCP) which negotiates the establishment and termination of a PPP link. LCP also negotiates the options for encapsulation format, authentication and link quality monitoring.

It is used together with ADSL for its ability to directly
connect the user to the Central Office of its ISP and its
important functions like: authentication, authorization,
automatic configuration of network interfaces and DHCP
support.

In the majority of European countries ADSL is based on
the ATM protocol so PPP is encapsulated inside ATM cells
(PPPoA). Nonetheless ADSL can stand on top of Ethernet and be
incapsulated in Ethernet Frames (PPPoE).

### Authentication
Authentication Option uses Password Authentication Protocol
(PAP) or Challenge Handshake Authentication Protocol (CHAP). The protocol used depends on negotiation. CHAP uses a one-way hashing algorithm which is known only to the
user, to respond to a challenge sent by the authenticator. CHAP is more secure than PAP.
# Qwestion and answer 

# Cuomo


## Describe the functions that are performed in the different functional areas of a network and the main network topologies used in that areas
A network is divided in functional area: **access network**, **core network** and **edge of the network**. The **access network** is the part that connect subscribers to their service provider. The access network play has an important role. It connect the communication carriers and the service providers with the entity that they serve. The **core network** is a backbone network **it usualli has a mesh topology** and provide any to any connection among devices in the network (it consist of multiple switches or ip routers). The internet is a core network composed by the interconnection of the core networks of all the service providers. Instead **the edge of the network** is were the networks and the end user exist. Usually has a star topology and this part of the networks may perform some intelligent fucntions that the core network does not perform. (the core network switch the packets instead the hop-by-hop routinf of the edge)

## Describe how modulation of the ADSL allow to use in an efficient way copper cables
The copper based network was designed mainly for carrying POTS (Plain old telephone systems). The ADSL provide for passive trasmission of analog voice service (POTS) and it can deliver from 8 to 24 Mbit/s (ADSL-ADSL2+). It uses two separate freqwuency bands (upstream and downstream). The dimension of these two band are asymmetric (Asymmentric Digital Subscriber Line). The downstream band is larger than the upstream this because is designed taking into account that when it was developed, the end users tended to use data from the internet instead of creating it. And it uses the DMT modulation that split the upstream and  the downstream in subchannels (tones). This subchannels can be manipulated individually taking into account the line conditions. For example if a subchannel has an external interference that cause error it may not be used in favor os other subchannels. **DMT can dynamically adapt the data rate to the line conditions**. It uses the **water filling** method in order to estimate the power o each subcarrier. And using this method we can assign the righ QAM costellation in each subcarrier in order to optimize the overall performance.

**CAP** is a versio of QAM, where the data are mudulated with a single carrier and then are trasmitted over the telephoe line. The carrier is suppressed before the trasmission because the carrier itself does not contain any information, and can be reconstructed at the receiver.

## What is MIMO and how it is used in the LTE and future generarions cellular systems
Multiple Input Multiple Output known as MIMO is referred to the use of multiple antennas as trasmitters and receivers side. It is used in LTE in order to reach the 100Mbps peek in downlink. MIMO has multiple modes of operatition for trasmit antennas. **Spatial multiplexing**, **Beamforming** and **single stream trasmit diversity**. The trasmit diversity permit to have multiple replicas of the same signal sent to several antennas, this permit to have an higher SNR at the Rx. The **spatial multiplexing** instead it permit to different data streams to be sent simultaneously on different antennas. And **beamforming** we can have two kind of beamforming: Phased array systems that have a finite number of predefined pattern (fixed) or we could have an adaptive array systems that have an infinite nymber of patterns that are adjusted to the scenario in real time. In the future generation of LTE there is an increment on the number of the multiple antennas used in the MIMO systems in order to increase the performance of the network. The benefits of the multiple antennas are: the simplifies multiuser processing and the reduced trasmit power.

## Key feature in LTE
The main feature in LTE are: **MIMO transmission**, **Adaptive modulation and coding AMC**, **Hybrid ARQ** and **Spectrum flexibility (OFDMA and SC-FDMA)**. With AMC using channel quality indication (CQI) (eNB send DL reference dignal, the UE send the CQI report and the eNB allocate band and use AMC in order to determine the correct modulation scheemes to use) the transmitter can **adaptively** determine the modulation and coding schemes. With the HARQ we conbine the FEC (forwaed weeoe correction) and ARQ (automatic repeat request) this means that if the received data has an error the receiver buffer the data and request a retrasmission af the data. When the retrasmitted data is received it the receiver combines the received data with the data buffered in order to reconstruct the packet this appens only with the FEC the error cannot be reconstructed. The **spectrum flexibity (OFDMA and SC-FDMA)** The current specification of LTE support subset of 6 different system bandwiths. The uplink (UE ti eNB) uses SC-FDMA (single carrier frequancy division multiple access) and in the downlink it uses OFDMA (ortogonal frequancies division multiple access). While SC-FDMA allocate user just in time domain OFDMA allocates user in time and frequency domain. The minimum allocation resource is a **Resouece block (RB)**. In OFDMA each sub carrier only carries information related to one specific symbol, instead in SC-FDMA each subcarrier contains information od all trasmitted symbols. Lastly we have the **MIMO trasmission**. With multiple antennas we can achive better performance using the Beamforming, statial multiplexing and singrle stream diversity. The beamgorming permit us to use at the same time multiple antennas in order to create a beam to improve the performance and in oder to focus the beam in only the direction af the end user in order to achive longher distance and because the beam is directional we dont have an interference in the air and so we can serve multiple user at the same time that is the spatial divercity. Instead with the spatial multiplexing generated by having myltiple antenna that are receiving the same signal we have free multiple replicas of the same signal because having the antennas displaced in different point we have myltiple stream of the same data and we can use this in order to have an higher SNR at Rx.  


## FTTx architectures and VDSL vectoring
There are many FTTx and the difference of one from each othe is where the fiber otics end adn start the copper (if there is the copper). The more the fiber optics is near the router the more the cohnnection can be fast. We can have multiple line of fiber optics (one for each user), or we can dave a single line that splits into multiple line to reach the end users, this operation of splitting the line can be done actively or passively. In this network the OLT (optical line terminal) manafet the entire bandwith in the downstream (point to multipoint proto) , instead in the upstream (multiple point to point network) the ONUs (optical network unit) trasmit only towards the OLT, and the ONUs cannot detecs other ONUS trasmissios. **The data trasmitted by the ONUs it may collide** so are used protocol that are designed for sistem like this such ad TDMA. Whit TDM-PON is used a beoadcast scheme in the downstream and TDMA in the upstream.

The VDSL vectoring is a method that permit to cancel the crosstalk by injecting an antisignal in each crosstalk impaired line. in order to work it requre a full synchronization over the vectored system. It require the calculation of the antisignal and on order to doing that it require a mechanism to derive the crosstalk coefficients. After this the antisignal is ingected trough the copper lines and reduce the crosstalk.

## PON architecture and how they work
The atchitecture of PON (passive optical network) implement a point to multipoint topology. These means that a single optical cable serves multiple endpoint using passive splitters. In the downstream the OLT manage the whole bandwith in order to have a point to multipoint network. Instead in the upstream we have a multipoint to point network. All the ONUs trasmit to the OLT, but the ONUs cannot detect other communication from ONUs and so the data trasmitted can collide. In order to avoid that we need a channel separation mechanism to share the bandwith resource in a fairly mode (every pays the same for the service so CSMA-CD no ok). We have the TDMA (time division multiple access ) and the WDMA (wavelenght division multiple access (siminal to the FDMA only for optical networks)). And so we have TDM-PON and WDM-PON

## describe the possible noise effect in a digital signal and how they are combatted
The main noise effect in a digital signal are generated when the phisical copper cable is bundled together with other cables (Binder group). In this binder group the multiple cables that interferes with each other can generate a cross-talk (interference causated by the electromagnetic field of each cable that induces a current in the other cabled in the binder group). The main schemas of this interference is the FEXT and the NEXT. The difference from this two schemas is the displacement of the TX and RX terminal. The interference generated by the FEXT is less powerful respect the intrerference genereated by the NEXT this because the interference in the FEXT schemes travel more than the interference in the NEXT interference. Because in the NEXT the TX terminal is near the RX terminal of the affected signal and so we have a sigal affected from the distance (the signal affected by the interference has alredy theveled to reach the RX and so the power of the signal is attenuated instead the interference signal due to the fact that the TX of the interference id near the RX of the affected signal is less attenuated by the distance). In the FEXT instead the interference signal travel side by side the affected signal and so also  the interference signal is attenyated by the distance. This interference can be attenuated using the Echo cancellation and the VDSL vectoring. The principle is similar both of the techniqes uses an anti signal of the interference in order to eliminate the interference in the channel.

## Witch are the key difference if the old use of the copper wire to procvide data (analog voice band modem) and the digital one
in the analog voice band modem the data was trasmittes as it was a voice phone call. The data trasmitted (56 kbps) cannot reach high spees velocity because was limited by the POTS bandwith. And also the data was a phone call and so it was impossible to make a phone call and surf to the internet at the same time. And more the distance from the modem and the co increases less the data rate that the modem can reach. Instead with the digital one as the ADSL the voice communication are separated form the data communication and so we can use both at the same time. And the bandwith is separated from the POTS and can reach faster speed 8 to 20 Mbps (ADSL, ADSL2+) by encreasing the bandwith (ad for the shannon C = B * log2 (1+SNR) encreasing the bandwith we are encreasing the channel bit rate).

## in the computation of the capacity that a channel can provide both the effect if the bandwith and of the SNR are present. Discuss how these have an inpact and how they can be managed to improve the capacity of the channel



# Polverini

## Provide a description of the fragmentation service in ipv6 explaining the pros and the cons of the used approach
In ipv6 the fragmentation of the packets can be done only from the senders. This in in opposition with the ipv4 that permit the fragmentation to the routers. The reason for this is that with this rule (fragmentation only from the source) we can reduce the overall delay in the trasmission, but in order to doing that we need to evaluate the MTU of the channel. and this is done with the MTU discovery. As we said before aS the pros we have less delay and less processing requires (the routes in this case do nothing for the frammentation) but ad the cons we have the need of an MTU path discovery procedure and we also are introducing some security threats. For example if we have some overlapping at the other end of the trasmission we could use this for inibit the power of an IDS to see an attack or similar. 


## Describe the security servicies provided by Encapsulating Security payload protocol, specifying the differences between the case in witch is used in transport or in tunnel mode
IP level of security includes tree areas. Autentication, confidentiality and key management. These can be done using some ip header: **AH** (authentication), **ESP** encrytion only and **ESP** with both encryption and authentication. Both there headers support tunnel and trasport mode. The tramsport mode provide pritection to the upper level protocols, for example the ESP header in transport mode encrypt and optionally authenticate the payload of IP but not the IP heafer, the AH in trasport mode authenticate the payload and some field of the IP header.
Instead in the tunnel mode the ESP or AH header privides pritection to all the IP packet. The entire packet are used as payload of a new IP packet (outher packet) and are used when one or both ends are a secure gateway.


                     
## Witch reference to the SDN architecture, provide a description of the data-control plane interaction generated by an application that provides the NAT service.
In a nat service we have a match for an ip address and port and an action to change the address and port. A SDN has a logical centered control plane for easier management of the network. We have at the **data plane switches** that have a switch flow table computed and installed by the **controller**. The **controller** id the operating system of the network. It mantain the state of the network, it **interact with the network control application** and with the **swotches** below, is implemented ad distributed system. At the top we have the **network control apps** these are the brain of the control plane. For example if we have a host H1 that are natted the packet from H1 reach the switch S1, if S1 has no rule for this event (the packet) the switch request to the controlled a flow rule for the packet (with missingflowrule events ) the controller than request to the application that provides the NAT service how handle the packet the application respond to the controller that respond and install to the switch the flow rule for that packet in order to avod all these passager the next time. After some delta t time  the rule installed if is not used is removed from the switch this for reducing the memory consumption. 

## Describe how trace route can discover the path taken from a packet toward a partcular destination
The traceroute command sent a list of packet incrementing the TTL of the packet starting from 0. When a router receive a packet wit TTL to 0 replay with a packet ICMP time exceded. using this packet and incrementing the TTL one by one each time the tracerote can estimate a plausible path that the packet are following.

## Ipsec/ike
IPsec is a secure network protocol that provide authentication and encryption between two endpoint. Two protocol are used: AH that provide the autentication and the ESP that provide encryption. Using ESP+AH we can have encrytion and authentication. The protocol uses security associations that are **one way** relationship between the sender e the receiver. If is required a peer relationship for two way secure excange two security association are required. A SA (sec association) is identified using 3 parameter. SPI (sec parameter index), IP destination address and Security protocol (AH and ESP). In each implementation af IPsec there is a Security Association Database that define the parameters associated with each SA. And there is also a Security Policy Database (SPD) that contain entries that define a subset of ip traffic and point to an SA containsed in the SAD. **So in the outbound traffic** the protocol first see the SPD in order to see wich policy has to be applicated to the IP traffic, and then see the SA inside the SAD in order to send the traffic. **Instead in the inbound traffic** the protocol first check the SAD and than the SPD. 

IPsec support both trasport and tunnel mode. In the transport mode only the payload of the ip packet is authenticated or encrypted, instead in tunnel mode the entire ip packet is encapsulated in a new ip packet and so the entire original ip packet is autenticated and encrypted. 

In order to combine the ESP and the AH header for have the encryption and the autentication we need to have conbination of SA (applying an SA on top to another SA).

The **automated** key management of the ipsec is referred to ISAKMP/Oakley. It consist od the Oakley key excange protocol gased in the diffie elman protocol and the ISAKMP that provide a framework for key management. The **IKE** protocol is an hybrid inplemantation of the Oakley/ISAKMP. It work with 2 phases: Establishes ISAKMP SA and then estalises the IPsec SAs. The IPsec protocol search in the SPD in order to determine id IPsec is enabled in the outgoing traffic. IS that does not have an entry in the SAD then the IPsec module request to IKE to establish the SA, the IKE negotiate and perform the excange with the peer in order to have the SA and then IKE insert the SA into the SAD.




