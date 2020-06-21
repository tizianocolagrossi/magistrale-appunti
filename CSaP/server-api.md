 # API server CSap 2019-2020 (whiteboard)

## Structure of whiteboard

- whiteboard (app)
    - unixconfig (topic)
        - thread1
            - reply
            - reply
        - thread2
            - reply
    - cyberpunk (topic)
        - thread1
            - reply
            - reply
            - reply
        - thread2
            - reply
            - reply


 ## command: authenticate
 **TODO**

 ## list messages 
 The server send all the messages of the topics to which the user is subscribed.  
 REQUEST
 > GET /messages

HEADER REQUEST 
```yaml
{
    "userid-or-token": "userid-or-token"
}   
```

 ---
RESPONSE
```yaml
{
    "status": 200
    "lenght": 3
    "data": [
        {
            "id": "123e4567-e89b-12d3-a456-426614174000"
            "topic": "unixconfig" 
            "from": "eurus"
            "text": "Wow is awesome!"       
        },
        {
            "id": "123e4567-e89b-12d3-a456-426614174000"
            "topic": "unixconfig"  
            "from": "isfet"
            "when": "2012-04-21T18:25:43-05:00" 
            "text": "This is my configuration of DWM"       
        },  
        {
            "id": "123e4567-e89b-12d3-a456-426614174000"
            "topic": "cyberpunk"  
            "from": "isfet"
            "when": "2012-04-21T18:25:43-05:00" 
            "text": "I'm so hyped for cyberpunk 2077"       
        },  
    ]
}  
```

## list [messages | topics] 
 The server send all the messages of the topics to which the user is subscribed.  
 REQUEST
 > GET /:topic/messages
 ---
RESPONSE
```yaml
{
    "status": 200
    "lenght": 2
    "data": [
        {
            "id": "123e4567-e89b-12d3-a456-426614174000"
            "topic": "unixconfig" 
            "from": "eurus"
            "text": "Wow is awesome!"       
        },
        {
            "id": "123e4567-e89b-12d3-a456-426614174000"
            "topic": "unixconfig"  
            "from": "isfet"
            "when": "2012-04-21T18:25:43-05:00" 
            "text": "This is my configuration of DWM"       
        },  
    ]
}  
```

## list topics
The server send all existing topics in the whiteboard app
REQUEST  
> GET /topics

HEADER REQUEST 
```yaml
{
    "userid-or-token": "userid-or-token"
}   
```

RESPONSE
```yaml
{
    "status": 200
    "lenght": 2
    "data": [
        {
            "topic": "unixconfig",
            "subsribed": true
        },
        {
            "topic": "cyberpunk",
            "subsribed": true
        }
        
    ]
}    
```

## get [message#]
The server send the message required by user  
REQUEST  
> GET /message/:id 

RESPONSE
```yaml
{
    "status": 200
    "data": {
            "id": "123e4567-e89b-12d3-a456-426614174000"
            "topic": "unixconfig" 
            "from": "eurus"
            "text": "Wow is awesome!"       
        }
}    
```

##  status [message#]
Server send the status af a given message (sent/published)  
REQUEST  
> GET /message/:id/status  

RESPONSE
```yaml
{
    "status": 200
    "data": "sent" | "published"
}    
```

## reply [message#]

Reply to a message to a thread.  
REQUEST 
> POST /:topic/threads/:id    

where topic is the **:topic** name and **:id** is the message id is the id of the message to which the user must reply.  
BODY REQUEST 
```yaml
{
    "data": {
            "from": "eurus"
            "text": "Wow is awesome!"       
        }
}    
```
RESPONSE
```yaml
{
    "status": 200
}    
```

## create [topic]
Create a new topic in the witeboard app  
REQUEST  
>POST /:topic  

RESPONSE
```yaml
{
    "status": 200
}    
```

## append [topic | thread]
Create a new thread in a topic  
REQUEST  
> POST /:topic/threads  

BODY REQUEST 
```yaml
{
    "data": {
            "from": "eurus"
            "text": "this is a new test thread"       
        }
}   
```

RESPONSE  
```yaml
{
    "status": 200
}    
```

## subscribe [topic]
Subscribe a user to a topic  
REQUEST  
> POST /:topic/subscribe

HEADER REQUEST 
```yaml
{
    "userid-or-token": "userid-or-token"
}   
```

RESPONSE
```yaml
{
    "status": 200
}    
```

## delete [topic]
Delete a topic only if the user is the topic's owner  
REQUEST 
> DELETE /:topic

RESPONSE
```yaml
{
    "status": 200
}    
```

# Status Code Used

code|meaning
---|---
200|OK
201|CREATED
204|OK NO CONTENT
404|BAD REQUEST
401|UNAUTHORIZED
404|NOT FOUND
408|REQUEST TIMEOUT
500|INTERNAL SERVER ERROR
