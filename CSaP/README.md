# CSaP - Project AA 2019-2020
## Goal
- Write a “whiteboard” application, where a community of users could interact and exchange messages.
- A server will wait for connections, on a INET socket, forking new processes to handle interactions with clients (e.g. there might be one process per client)
- Interacting with the application, users will:
    - Authenticate
    - List, Subscribe and create topics
    - Append message to one topics
    - See the satatus of sent messages (recived/published)
    - Receive (and reply to) messages, by posting to topic

#### NOTES
- For the implementation, use processes (not threads) and SYSV IPCs (not POSIX semaphores, etc).
- Once a message is published to a "topic" all subscribed users will be able to get a copy of it
- Messages cannot be edited or deleted once sent/published
- Users are added/deleted to the system by an external administrator which manages credentials

## Client Commands
- authenticate -> reads and sends user name and password to server
- list [messages | topics] -> get list of messages, read or unread, ordered by topics
- get [message#] -> receive and display a message on user console
- status [message#] -> display the status of a specific message
- reply [message#] -> append a new message to a thread (in a topic)
- create [topic] -> create a new topic (user will be the owner)
- append [topic | thread] -> appends a new message to a new thread in topic
- subscribe [topic] -> insert the user in the list of recipients for this topics
- delete [topic] -> delete a topic and all messages **only if owner of topic** 

# Evaluation Scorecard
- the code works
- race condition
- robustness under unexpected situations
    - misbehaved clients
    - communications errors
    - reboot/crashes
- implementation of new commands

## project collaterals
- source code, including for each function/global variable/data structure:
    - purpose
    - parameters, side effects and return value (for functions)
- **DOCUMENTATION** describing:
    - Design Choices (and the reasoning behind them)
    - Macro modules and their interactions
    - Test cases
    - Release notes, including lmitations, known errors, etc...

## Suggestions Be Creative
- evaluate all possible alternatives, but (as a starting point
    - the whiteboard data structure shoul reside in a shared memory and protected with SYSV semaphores
- apply the KISS principle (Keep It Simpe, Stupid)
- start prototiping early, use scaffoldings (e.g. funtions returning just a plausible value, etc.. )
- few days before deadline, send a draft of code and documentation for review/interaction 
