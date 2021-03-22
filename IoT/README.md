# IoT

## Terminology
- **BER** Bit Error Rate
- **Diffraction** surface sharp edges (bending the wave)
- **Scattering** Wave encounter obj smaller than the wavelenght
- **Line of sight** Straight line between tx and rx
- **Reflection** Path between tx and rx reflected from floor or other
- **Shadowing** rx hidden by wall or obstacle 
- **Network lifetime:** Time till the first node in the network dies having depleted its battery or Time before the network gets disconnected or fails to perform critical tasks
 
# Fundamental of wireless systems
**In a city a LOS(Line Of Site) non necessarily exist** we can take advantage of Diffraction and Reflection.

The signal fade exponentially with te distance.


Directivity (D) = ( power density at distance d in the directin of MAX radiation / mean power density at distance d )


Gain (G) = ( power density at a distance d in the direction of MAX radiation / (P_t / 4Pid^2) ) * k


K = antenna efficency factor (<=1)


P_t = trasmission power


Power density at distance d = F = P_t / 4Pid^2


Received power densitiy in the trasmission in direction of MAX radiation = F = P_t *g_t / 4Pid^2   with g_t maximum transmission gain.


P_t*g_t is the EIRP (Effective Isotropically Radiated Power) represents the power at which an isotropic radiator should transmit to reach the same power density of the directional antenna at distance d

Signal replicas received via different propagation paths are combined at the receiver The result depends by the numbers if replica, their phases, their amplitude and their frequency. The resulting signal can be attenuated or amplified.

###### **slide intro end**

# Techniques for energy efficient communications

Portable devices rely on external sources of energy (batteries, solar cells) to be able to communicate. **And battery life is limited**

Despite improvements in battery technologies the problem
has not been solved (and is not expected to be solved by
better battery technology only):
- energy demand is increasing
- users expectations in terms of device/network lifetime are increasing
  
And so energy efficient techniques have been developed. **Energy consumption is a critical metric driving wireless systems design**

**Network lifetime:**
- Time till the first node in the network dies having depleted its battery
- Time before the network gets disconnected or fails to perform critical tasks (e.g., coverage of an Area of Interest)

### Energy consumption components
Laptop most energy consuming components include CPU, liquid crystal display (LCD) and **wireless network interface card**

### Energy-efficient techniques
Network-related energy consumption has two components
- Computing: in network data processing, data fusion and aggregation, protocol operations
- Communications: Wireless transceiver consumes energy either to transmit/receive data and control packets, or when it is idle, ready ro receive

Trade-off between computation and communication. There is an inherent computing vs. communication trade-off. **Where should the ‘intelligence’ of the system be placed? Which data should be processed in network**
( -> **higher energy consumption due to computing** in nodes which can be energy constrained, but -> **more compact data transmitted, thus lower energy consumption due to communication**) and which data should instead be transmitted to “higher end” devices or computing systems for processing ?(e.g., to the base station, to the sink, or which tasks should be offloaded to the cloud)


The objective of the energy efficient communication techniques is to optimized these trade-offs, and the trade-offs amongs different E2E performance metrics (not just energy consumption but also throughput, latency).

#### General guidelines

- Power consumption is a function of the energy needed to activate the transceiver circuitry and of the emitted power **we can significantly decrease overall energy consumption in case of long range communication by applying power control Objective: minimizing transmission energy**
- Wireless technologies can dynamically change the modulation scheme used over time. Use of high data rate modulations reduce the time needed to transmit packets, thus the associated transmission energy consumption **(Objective: minimizing transmission energy**
- HW-dependent optimization and selection of HW: due to design choices standard compliant transceivers can have quite different performance in terms of energy consumption, BER and PER (Bit and Packet Error Rates). **HW selection can thus significantly impact the overall system energy consumption**


(TO CHANGE)

- Promiscous mode: several protocols proposed for ad hoc network routing exploit the idea of operating the wireless interface card in promiscous mode ( àreceived packets are passed to higher layers and processed even if not addressed to the node) in order to gather information over the wireless broadcast channel which can be used to optimize the protocol operations. **BAD**
- Operating the wireless interface card in promiscous mode forces the interface card to stay in idle (instead of low power modes) for long periods of time, and leads to significant energy consumption due to processing of packets. Therefore, its use typically is a killer in terms of overall energy consumption **BAD**


(CHANGE IN)
- Wireless transceiver should instead be switched to a low power ‘sleep state’ (where it cannot receive or transmit packets but the energy consumption is orders of magnitude lower) whenever a packet not addressed to the node or whenever information exchanged during a handshake make the node aware that the channel will be busy for the next future for transmitting packets not addressed to it
- The transceiver should switch to low power mode for the whole time interval when it knows it will not be involved in communications.
  - This is also why destination address is the first field of the header
  - This is also why NAV field is part of RTS/CTS handshake in IEEE 802.11


- MAC
  - **Awake/asleep schedule:** Nodes alternate between (**increase latency**)
    - high energy consuming states (awake:transmit/receive/idle) in which the transceiver is ON and packets can be transmitted/received AND
    - states in which the transceiver is OFF, packets cannot be received or transmitted but the energy consumption is much lower
    - Duty cycle=T_ON/(T_ON+T_OFF)
    - Two possible classes of protocols
      - **Synchronous:**  nodes exchange information to coordinate on when to wake up, periodic control message exchange ensures they know when their neighbors will wake up, a packet is transmitted to a neighbor when it is ON.
      - **Asynchronous:** Awake/asleep schedule of neighbors is unknown, No control overhead is needed to keep information updated, To ensure reliable communications a sequence of packets must be sent until the destination node wakes up and answers (overhead when a packet has to be sent). OR nodes must follow a cross-layering approach selecting one neighbors among the awake neighbors as relay.
  - Nodes not involved in communication should go to sleep till current information exchange completes **(Objective: avoid energy waste)**.
  - Nodes should minimize collisions **(Objective: avoid energy waste)**.
  - Header compression: By transmitting less bits the transceiver is ON for less time **(Objective: reducing transmission energy)**
  - Limit control information exchanged, aggregate redundant information **(Objective: reducing transmission energy)**


Transceiver states:
- **TX** Awake and transmitting
- **RX** Awake and receiving
- **IDLE** Awake, neither transmitting nor receiving
- **ASLEEP** Asleep: the transceiver is not operational but energy consumption is low. There can be several asleep states with different subsets of the circuitery switched OFF


General guidelines **Data Link**:
- If channel is in a bad (deep fade) state it is convenient to delay transmissions as it is very unlikely packets will be correctly received **(Objective: avoid waste)**
- Energy efficient ARQ and FEC schemes have been studied to optimize energy consumption while ensuring reliable and timely communication **(overhead vs. number of retransmissions trade-off; adaptive solutions depending on load, channel, application requirements).**


General guidelines **Routing**:
- Depending on the scenario it can be more energy efficient to transmit over a higher number of shorter links or minimize the number of hops **( Long range vs. short range communication)**
- Minimize the overhead associated to route discovery and maintenance
- Load balancing of the energy consumption among nodes to increase the network lifetime
- Energy aware routing solutions which account for residual energy (and expected future availability of energy in case harvesting is an option) when selecting the best next hop relay
- Link quality aware relay selection to avoid retransmissions
- Relay selection which favors data fusion/aggregation
- All the above combined <- cross layer solutions

###### second slide end

