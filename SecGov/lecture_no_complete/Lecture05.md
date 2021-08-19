# Lectore 5 (CYBER-RISK MANAGEMENT)

## Cyber-Systems

A **cyberspace** is a collection of interconnected computerized networks, including services, computer systems, embedded processors, and controllers, as well as information in storage or transit. 

A **cyber-system** is a system that makes use of a cyberspace.

A **cyber-physical system** is a cyber-system that controls and responds to physical entities through actuators and sensors.

## Cyber-Security

**Cybersecurity** is the protection of cyber-systems against cyber-threats.

A cyber-threats can be **Malicious** or **Non-Malicious**

- **Malicious** Caused by intention (DoS etc)
- **Non-Malicious** Accidental (crash, programm errors, internet connection etc)

## Cybersecurity VS Information Security

![](img/cyber_info_sec.png)

## Risk Management vs Cyber-risk Management

The main difference between Risk Management and Cyber-risk Management is in the Risk assestment:

1.  **Context Estabilishment**: La superficie di attacco (Attack Surface) `e costituita da tutti i diversi punti in cui un aggressore o un’altrafonte di minaccia potrebbe entrare nel Cyber-system e dove le info ed i dati possono uscire.

The **attack surface** is all of the different points where anattacker or other threat source could get into the cyber-system, and where information or data can get out.

Typical assets of concern in the setting of cyber-riskassessment are:
- information
- information infrastructures(including software, services,and networks)

So we have to list all the assets to protect and even the reputation should be listed and maybe the machine that runs our service should not be listed because it is the service itself an asset and not the machine that runs it. 



2.   **Risk Identification**: Identificazione di incidenti dannosi.Indagare su come le minacce dannose possono causare danni alle risorse identificate, date le vulnerabilita identificata.  L’aiuto e la guida provengono da:
- registri degli eventi relativi a precedenti rilevanti;
- indagine sui tipi di incidenti che le minacce e le vulnerabilita possono portare sulla base dei risultati delleattivita di pen test.
  
**Identificazione di incidenti NON dannosi**.

Gli incidenti e gli atti non intenzionali sono spesso ricorrenti e noti.  Utilizzare registri, dati monitorati ed altri dati storici per supportare l’identificazione. 

![](img/risk_model_attack.png)

For the malicious threat source identification is important to understand:
- who may want to initiate the attack (a competitor,a solo hacker, an activist group andso on) and if it is a person or a software because this will be very important
- what motivates them
- what are their capabilities and intentions
- how attacks can be launched

In this way we create a profile for the attacker and we always need to remember that threat and threat sources are not just external, they maybe inside our cyber-system! Not only because the employee wants to directly attack its company but also because due to its low attention regarding security may lead to an external attacker to access from the inside (thinkabout password on post it, user that attach pen drive on working PC etc.).

At this point we have to focus on the identified attack surface and identify malicious vulnerability that can be exploited.

By investigating existing controls and defence mechanisms to determine their strength and adequacy with respect to the identified threats and assets.

Byrunning security testing i.e., penetration testing and vulnerability scanning:
- to check whether or how easily a specific threat source can actually launch an attack
- to investigate the severity of known vulnerabilities,
- to look for potential vulnerabilities, and
- to look for possible incidents that the maliciousthreats may lead to

We are now enabled to identify malicious incidents investigating how the malicious threats can cause harm to the identified assets given the identified vulnerabilities

Instead for non malicious cyber-risk we have to remember that usually there is no intent or motive behind a non malicious risk. Because in this case it is not practical to start by identifying and documenting threat sources it is recommended to start from the valuables to be defended.

![](img/risk_model.png)

3. **Risk Analysis**. 
   We are now ready to **analyze the likelihood and impact of the risk**.

   Sono due gli aspetti che differenziano l’analisi del rischio informatico con l’analisi dei rischi in generale:

   1.  Per le malicious threat dietro le quali si nascondono intenzioni e motivi umani puo essere difficile stimare la probabilita che si verifichi un evento;
   2.  A causa della natura dei sistemi informatici abbiamo diverse opzioni per la registrazione, il monitoraggio ed i test che possono facilitare l’analisi.

There are different techniques for risk analysis, one of them is threat modelling to describe aspects such as attack prerequisites.

4. **Risk Evaluation** very similar to the general case.

![](img/cyber_risk_eval.png)

5. **Risk Treatment**.

In particolare, vi sono due caratteristiche che distinguono il trattamento del rischio dei sistemi informatici dal caso generale:

   1. la natura altamente tecnica dei sistemi informaticifa si che le opzioni per il trattamento dei rischi siano anche tecniche
   2. la distinzione  tra  rischi  informatici  malevoli  e  non  malevoli ha  implicazioni  per  il  trattamento del rischio piu adeguato
