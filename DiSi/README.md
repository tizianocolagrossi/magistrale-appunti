# Distributed System

## todo recover notes before quorum 


### QUORUM

**objecitve:** Build a URB in FAIL-SILENT. **IDEA TO IMPLEMENT** whenever we have a uniform reliable brodcast, is to be shure that at least a correct process will receive our message. If we know thar our message has reached a correct process and this correct process start a **best effort brodcast** we know that every other **correct** process will receive the message for the property of best effort.

The idea that we use to achive this, to build this algorithm, is the idea of a **quorum**. A **quorum** in a case of crash tolerant system is a **majority of processes**, a quorum is any subset of our total processes P such that the size of this subset is at least **n/2-1**. **ANY two quorums intersect in at least one processes**.

**IDEA**
- C set of **correct** process
- F set pf **faulty** process
  
**Remember we do not know who is C and who is F**. But if we assume F < n/2 (i.e majority of process are in C) then **Any quorum Q of P contains at least one correct process** (proof Q is a quorum of P and C is a quorum of P). **If we wait for a quorum of ACK we know that at least one of this process i correct**.

**RECAP**
- **IDEA1:** if i know that one correct process has seen a message then i can deliver it safely. **ASSUMING** that F<n/2 is reasonable (rare) **then**
- **IDEA2:** if a quorum of processes see a message, then this message is safe to be deliver. 

**UNIFORM RELIABLE BROADCAST** we have seen that:
- exist an algorithm for synchromous system using **perfect failure detector**.
- exist an algorithm for asynchronous system when assuming a **majority of correct process**.

**problem with this kind of algorithm is the number of message that are send** we want havo something that scale better with thw complexity of the system. we want some algorithm that send less than n^2 messages.

## Probabilistic Broadcast
**gossipping technique** equivalent to the best effort but is probabilistic. Gossip dissemination. 
- A process send a message to a set of randomly chosen k process.
- A process receiving a message for the first time forwards it to a set of k randomly chosen processes (this operation is also called a round).
- The algorithm perform a maximum number of r rounds.

## Ordered Comminication
**Try to define guarantees about order of deliveries inside group of processes** We want this type of ordering:
- Deliveries respect the FIFO ordering of the corresponding send (local ordering)
- Deliveries respect the Causal ordering of the corrisponding send
- Deliveries respect a total ordering of deliveries (atomic communication, we will see this after the consensus)

## FIFO Broadcast 
It can be regular or uniform (**IT HAS NO ORDERING ACROSS PROCESS** local ordering)

### **module specification**:
**Name:** FIFOReliableBroadcast, **instance** *frb*.
#### **Events:**
**Request:** <*frb*, Broadcast | *m* >: Broadcast a message *m*  
**Indication:** <*frb*, Deliver | *p*, *m* >: Delivers a message *m* broadcast by process *p*
#### **Properties:**
**FRB1-FRB4:** Same as properties RB1-RB4 in a (regular) reliable broadcast by process *p*  
**FRB5:** *FIFO delivery:* if dome process broadcasts a message *m1* before broadcasts message *m2*, then no correct process delivers *m2* unless it has alredy delivered *m1* 

**But some time FIFO is not enough**

## Causal order Broadcast
Guarantees that messages are delivered such that they respect all cause-effect relations. (*causal order is an extension of the heppaned before relation*) a message *m1* may have potentially caused another message *m2* (denotes *m1*->*m2*) if any of the following holds:
- some process *p* delivers *m1* before broadcasts *m2*
- some process *p* delivers *m1* and subsequently broadcast *m2*
- there exist some message *m'* such that *m1* -> *m'* and *m'* -> *m2*
  
### **Module specification:**
**Name:** CausalOrderReliableBroadcast, **instance** *crb*.
#### **Events:**
**Request:** < *crb*, Broadcast | *m* >: Broadcast a message *m* to all process.
**Indication:** < *crb*. Deliver | *p*,*m* >: Delivers a message m broadcast by process p.
#### **Properties:**
**CRB1-CRB4:** Same as  properties RB1-RB4 in (regular) reliable broadcast.  
**CRB5:** *Causal delivery:* For any messages *m1* that potentially caused a message *m2*, i.e., *m1* -> *m2*, no process delivers *m2* unless it has alredy delivered *m1*

one implementation for this kind of algorithm is similar to the concept of the vector clock. When a process send a message, send also all his history of the messages received, and when the other process receive this vector of messages deliver all the messages that it has not delivered yet.

There are a inproved version of the non wait crb that has a ack mechenism for remove the message from the history send when the message is received from all the correct process.
