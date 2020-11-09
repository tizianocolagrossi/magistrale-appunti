# Distribited System

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

