# Describe the main objectives of the NIST CyberSecurity Framework and its structure
Trough the CEA (Cybersecurity Enancement Act) the NIST (National Institute for Standards and Tecnology) must
identify a repetable, flexible, cost effective and performance based approach, including security controls 
that can be used by owners of critical infrastructure to identify, manage and respond to cyber risk.

The NIST Cybersecurity Framework (CSF) focuses on using the buisness drivers to guide the cybersecirity
activities and making the cyber risk part of the risk management process.

The CSF final goal is to help organizations that want to introduce some cybersecurity policy into the 
organization from the risk perspective.

This framework (CSF) was developed to improve the cybersecurity risk management of critical infrastructures
and is intended to be used in organization that rely on tecnology such as: IT infrastuctire, IoT, Cyber-phisical
systems or industrial controls.

The main feature of the CSF is that is techology-neutral, in the sense that it does not focus on the specific
technology to protect, but it abstract the tecnology to identify what is the mai problem that can occur in this
context.

**Notice that the framework is not used to compute a risk, but is a tool that the organization uses to address**
**the critical elements that need to be protected and should be considered in the computation of the risk**
**and can also be used to identify the level of risk that the organization can accept.**

The CSF is composed by three parts: CORE, IMPLEMENTATION TIERS and PROFILES.
The **CORE** of the framework provide a set of activities to achive a specific cybersecurity outcome and
address some example that show how to reach it.
It is **composed by 4 elements** that are:
- **Functions**: Identify, Protect, Detect, Respond, Recover
- **Categories**: contribute the definition of functions and subdivide the functions in group of elements
- **Subcategories**: specoalization of the categories
- **Informative reference**: point to existing standards and best practices that the organization can use to compare

The **IMPLEMENTATION TIERS** take an orthogonal perspective, while in the core we try to identify which are the assets
that we should consider, the implementation tiers provide come criteria to evaluate the security defense of the organization
in term of "maturity of the process" (It check how the organization has structured the process).

It does not say nothing about the quality of the governance put in place by the company
but instead it evaluates how much is structured the process.


The **PROFILES**  is the alignment of the Functions, Categories, and Subcategories with the
business requirements, risk tolerance, and resources of the organization.

They can be used to describe the current state or the desired target state of specific
cybersecurity activities.

- The Current Profile indicates the cybersecurity outcomes that are currently being achieved.
- The Target Profile indicates the outcomes needed to achieve the desiredcybersecurity risk management goals.

The CSF can be used for different reasons, for self-assestment,improve or establish a cybersecurity policy or
for help to chose what an organization should buy (what should buy and what the organization candevelob by itself)

# Describe the structure of the NIST CSF and explain how it can be used to plan investments related to cybersecurity

For the first part seme as abow

For plan investents related to cybersecurity the CSF can be used for:
- improving the cyber seciruty program: so what is the level that the organization want to reach in a specific function
- evaluate what the organization should buy and what can be created by the organization related to the found that the 
  organization has.

So it can be used to plan investments related to the creation or improvement of the security program
of a company and also to decide what the company should buy(as a service from other companies for
example) and what instead we can create inside the company on our own.

#  Discuss the role of Best Practices and Standards in the design and realization of an Information Security Governance system

ISG should help in "doing the right thing in the right way" and every Information Security Manager should be able to 
know how are the right tings and how know that he is doing in a right way.

And in order to doing that he use the best practices. the best practices (or standards) are documents that
include the experience and the solution of other ISM (on which expert have consensus on it) that provides
and internationally accepted framework that can be used as bulding block for ISG.

Example of best practices are NIST or ISO-27k family.

We have seen in particular ISO 27001 and 27002 as examples of best practices.

ISO 27001 is an international standard that defines a set of reqirements to establish, implement, mantain and 
continually improve an ISMS within the context of an organization.

ISO 27002 is and international standards that is intended to be used by organization that want to:
- select controls within the process of implementing and ISMS based on ISO/IEC 27001
- implement common accepted information security management guidelines
- develop their own information security management guidelines

**Each clause defining security controls contains one or more security categories**, where each of them
contains a control objective stating **what is to be achieved and a set of controls that can be applied to
reach this objective**.

The difference between those two standards is in the fact that ISO 27001 is very strict and goes down
in detail, while ISO 27002 is more high-level and provides help to companies that want to build an
ISMS based on the experience of other companies.

# With reference to the depth dimension of Von Solms’s ISG model, describe the main characteristics of the Directive vertical block
An information security governance is needed for buisness. IT security governance is the system used 
from organization to direct and control IT security. 

One model to to implement a good ISG is the Von Solm model.
In the first layer in depth of the model we have the Directive part.

Security policy documents are needed by standards
and best practice. In order to be compliant with the requirements its fundamental to define a mechanism 
to create, manage and distribute policy realted documents.

The architecture of Information security policies is composed by: the Board Directives (identify the 
IT assets and provide a mandate to protect those assets), the Corporate Information Security Policy
(high level documents that provide the basis for alla the lower level documents related to info sec aspects)
, the Sub policies and Procedure (define how the sup policies should be implementes).

The CISP (Corporate Information Security Policy) should be less than 4 pages usually and should
be easy to read and be losely related to assets that can vary very often in order to be stable and
not change often.

The CISP should specify which is the owner of the policies and which are the people subjected to the 
policy. 


# With reference to the depth dimension of Von Solms’s ISG model, discuss the front dimension and its two core principles
The front dimension of the Von Solm model for ISG represent the execution of the processes and action of the 
Direct and Control loop in those processes.

The core part is based on two core principles: 

The first is that this Direct and Control loop covers all 3 level of management. 
1. **Strategic Level** is the one of the Directors that define one or more **objective**.
2. **Tactical Level** composed by manager, that define in which way the objectives should be reached,
   so they define a set of **procedure** to follow
3. **Operational Level** where things are actually done putting in place the procedures.

The second core principles is that across those levels there are some action: Direct, Execute and Control.
The Direct action start from the strategic level and goes down till the Operational Level, so in the Direct
action after the obhective are find are trasposed into policies and procedures. The strategic level identify 
the assets, their relevance and their required level of protection and they do this taking into account exteral factors.
Then the objective are taken as input by the Tactical level thattrasform this objectives into a set of policies and procedures.
Finally the Operational level take as input the policies and procedures and define how the things must be done in practices.

Then the Procedures are **Executed** 

After from the bottom to upper level there is the Control activity. And for this activity is important know that:
“we can manage only what we can measure” so to properly control we need to measure and to do it correctly we need 
to know which kind of information and data to collect.

The Operational level extracts some measurement data from different entities and provides a
technical report to the tactical level that will “translate” it into a Tactical Management report indicating
the level of compliance of the approach.
At the end the Strategic level has all the information required to understand if the objective that they
generated was successful or if they need to change something and restart the Direct and Control
loop.

# With reference to the depth dimension of Von Solms’s ISG model, describe the main characteristics of the Awareness with particular emphasis on the SETA program

We know that **all the levels are involved in the Information Security Governance process**, so, **every**
information **user** of the company **must be trained** and aware about the policies, procedures and
practices that we put in place.

The SETA(Security Education Training and Awareness) is an extension of the knowledge that people
already have to do their job and the extension comprehends the skills on how to do their job securely.

The goals of the SETA are:
1. improve the awareness of the importance and need to protect organizational information resources
2. acquire the necessary skills and know how to do their jobs more securely
3. create an understanding and insight into why it is important to protect organizational information assets

The employees must be trained about aspects such as why information is such an important asset,
and these training should typically address issues such as, user identification and authentication, so
everything related to the password management ranging from how to choose a strong password up to
how to store it(not on post-it left on the desk), legal usage of software, virus control, and so on.

# With reference to the threat modelling, describe the asset-centric, the attack-centric and the software-centric approaches highlighting for each of the advantages and disadvantages
Threat modeling is a structured approach to identifying, quantifying, and addressing threats.

It allows system security staff to communicate the potential damage of security flaws and
prioritize remediation efforts.

The **asset-centric** tries to identify what can go wrong taking a double perspective, one focusing on
what is important for the defender, and one for what is important for the attacker.
Note that the things that are important for the attacker are usually tangible things, while the ones for
the defender are not tangible(like the reputation of the company).

So we should consider all the enabling steps to reach the goal, which are all the elements that the
attacker may want, what you want to protect and that could be an instrument for reaching a higher
target for the attacker(the attacker may perform some lateral movements to reach its goal).
The steps to implement this type of threat modeling are first identify and list all the assets and then
consider how an attacker can threaten them, the connect each item in the list with a computer system
or a set of systems and at the end draw the system showing the assets and other components as well
as interconnections until you came up with a concrete solution.

PRO: if you want to support the risk by putting the emphasis on the business component from the
asset side you should use this technique.
CONS:

The **attacker-centric** is a type of threat modeling that takes the point of view of the attacker(think as an
attacker), so it first identifies which are all the possible attack techniques, analyse and review them to
identify which is the set of possible attackers and how these attacks can be applied on our system.
This procedure is performed by the analyst manually and typically it allows them to identify threats
that try to exploit human vulnerabilities(not software ones).

PRO: if you are trying to identify remediation plans or you are trying to provide more awareness you
should use this one.
Also, it is more useful when you need to come up with the human side of the system.
CONS:

The **software-centric** approach involves the design of the system and can be illustrated using software 
architecture diagrams such as data flow diagrams (DFD), use case diagrams, or component diagrams.

This method is commonly used to analyze networks and systems and has been adopted as the de-facto standard 
among manual approaches to software threat modeling. 

starts from the idea that from the point of view of the
programmer, the software that you have is different from the ideal software that you want essentially.
An ideal software should be defined by the set of requirements that generate it and the set of features
that we put in place in order to cover those requirements.
Practically speaking this does not happen, in fact, at the end we have a software that covers some of
the requirements but then others are not covered.
Also, the final software may have additional features that were not required but the developer thought
that these would be useful - these are called hidden functionalities, and since they are not required
are also usually not documented.
Moreover there can be designing bugs that make some features not working properly.
When we perform this type of modelling we have to keep in mind that the software is not just the
application, but also the OS on which it is run and the network that it utilizes to communicate.
So we need to focus on the application, but then look also at how the application is run on the
OS(maybe there may be buffer overflow vulnerabilities) and how it uses the network to communicate.
Ideally, this type of analysis should be done during the designing phase of the software, but in reality
this is usually done in the maintenance phase.

PRO: if you want to protect your system, so you are in the designing phase and you want to check
that the security features are implemented and the system will not be subject to specific threats, then
you'll need this technique.
CONS:


#  Describe the main phases of the Incident Management Process putting particular emphasis on the organizational structures and professional profiles involved

# Describe what is a SOC, its main responsibilities and design principles.

# Describe what is an attack graph and its three main usage scenarios

# Discuss a taxonomy of IDS systems putting particular emphasis on the different techniques that can be used to perform the analysis