# Distributed System

## Dictionary
- **Agreement properties**: If a correct process deliver a message then **every** other correct process deliver the same message.
- **Causal delivery**: For any message *m1* that potentially caused a message *m2*, i.e *m1*->*m2*, no process delivers *m2* unless it has alredy delivered *m1*.

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

## Total Order Broadcat
A **total order broadcast** abstractions orders all messages, **even those from different senders and those that are not causaly related**. The total order broadcast  abstraction is sometimes also called  **atomic broadcast**. The message is delivered to **all or none** of the processes and if the message is delivered, every other message is ordered either before or after this message. **Total order is orthogonal with respect to FIFO and causal order**. The computation is totally ordered bot not is it in FIFO.

### **relationships among broadcast specifications**
1. **Reliable Broadcast** ->total order-> **Atomic Broadcast**
1. **FIFO Broadcast** ->total order-> **FIFO Atomic Broadcast**
1. **Causal Broadcast** ->total order-> **Causal Atominc Broadcast**  
From 1. to 2. we add **FIFO Order** and from 2. to 3. we add **Local Order**

## Distributed Register
A register is a shared variable accessed by processes through read and write operations. Register absraction support the design os distributed solution, by hiding the complexity of the undelying message passing system and the distribution of the data.

### **Assumption**
- A register stores only positive integers and it is initialized to 0 (or null)
- Each written value is univocally identified. Similar to assumption of unique messages.
- Processes are **sequential**: a process cannot invoke a new operation before the one it previously invoked (if any) retourned.
  
**Failed operations**: is an operation invoked by some processpi that crashes before obtaining a return.

### **Operation precedence**
- The execution of an operation invoked by a process *p*, is the time interval defined by the invocation event and the return event.
- Given two operations *o* and *o'*, **o precedes o'** if the return event of *o* precedes the invocation event of the *o'* event.
- An operation *o* invoked by a process *p* may precede an operation *o'* invoked by *p'* only if *o* completes.
- If no precedence relation between two operations can be defined, they are said to be **concurrent**.
  
### **Registers semantic**

#### **Strictly Serializad Semantic**
- Assumptions
  - Serial Access: a process does not invoke an operation on the register if there is another process that previously invoked an operation on it and this latted did not complete yet.
  - no failures
- Specifications:
  - Liveness: Each operation eventually terminates.
  - Safety: Each read operation returns the last value written.
  
Semantic has to specify how we should behave in cases of failure or concurrency.

**GOAL: Define a set of useful semantics to cope with concurrency. The semantic restricts the set of allowed executions**

- Regular consistency
- Sequential consistency (REALLY HARD) we only define the semantic 
- Linearizability/ atomic consistency

 Those are the tree semantics that are we going to discuss during this course.

### **Register notation**
(X,Y) denotes a register where X processes write and Y processes can read. Ex (1,1)denotes a register where only one process can wite and only one process can read. **It is a priori know which pricess can read and which process can write**. **(W.R)**

### **(1,N) Regular register specification**
**Termination**. If a correct proceess invokes an operation, then the operation eventually receives the corresponding confirmation.  
**Validity**. A read operation returns the **last value written or the value concurrently written**.

**NOTE: in a regular register, a process can read a calue v and then a value v', even if the writer has vritten v' and then v, as long as the write and the read operation are concurrent**.

### **Regular register: interface**
**Name:** (1,N)-RegularRegister ,**instance** *onrr*

#### **Events:**
**Request**: <*onrr*, Read>: invokes a read operation on the register.  
**Request**: <*onrr*, Write | *v*>: Invokes a write operation with value *v* on the register.  
**Indication**: <*onrr*, ReadReturn | *v*>: Completes a read operation on the register with value *v*.  
**Indication**: <*onrr*, WriteReturn>: Completes a write operation on the register.

#### **Properties**
**ONRR1**: *Termination*: If a correct process invokes an operation, then the operation eventually completes.  
**ONRR2**: *Validity*: A read that is not concurrrent with a write returns the last value written, a read that is concurrent with a erite value returns the last value written or the value concurrently written.

**MAIN IDEA**: Each process keeps a local copy of the register (an integer initialy set to 0 or null). The single writer has to ensure the consistencies of copies. When I read I read from my local copy.

#### **Fail-Stop Algorithm:**
Processes can crash but the crashes can be reliably detected by all the other processes. **USES**: 
- Perfect Failure detector
- Perfect point to point link
- Best effor broadcast
  
##### **Algorithm idea**
- Each process stores a local copy of the register
- Read-One: each read operation returns the value stored in the local copy of the register.
- Write-All: each write operation updates the value locally stored at each process the writer consider to have not crashed.
- A write completes when the writer recives an ack from each process that has not crached
  
