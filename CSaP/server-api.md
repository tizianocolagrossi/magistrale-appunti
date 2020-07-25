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

 ## list topic sub (for list messages )
 The server send all the topics to which the user is subscribed.  
 REQUEST
 > GET /messages

HEADER REQUEST 
```yaml
{
    "username": "eurus",
    "token": "bdsakjldj*ghdsjal"
}   
```

 ---
RESPONSE
```yaml
{
    "status": 200,
    "sub": [
        "unixconfig" 
    ]
}  
```

## list [messages | topics] 
 The server send all the messages id of the topics selected.  
 REQUEST
 > GET /:topic/messages

HEADER REQUEST 
```yaml
{
    "username": "eurus",
    "token": "bdsakjldj*ghdsjal"
}   
```


RESPONSE
```yaml
{
    "status": 200,
     "id": "123e4567-e89b-12d3-a456-426614174000",
     "topic": "unixconfig",
     "owner": "eurus",
     "thread_id" = [
        "di%s!o",
        "5j*dkp",
        "h&tsk#",
        "l8t7rb",
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
    "username": "eurus",
    "token": "bdsakjldj*ghdsjal"
}   
```


RESPONSE
```yaml
{
    "status": 200
    "lenght": 2
    "data": [
        "unixconfig",
        "cyberpunk", 
    ]
}    
```

## get [message#]
The server send the message required by user  
REQUEST  
> GET /messages/:id 

HEADER REQUEST 
```yaml
{
    "username": "eurus",
    "token": "bdsakjldj*ghdsjal"
}   
```


RESPONSE
```yaml
{
    "status": 200,
    "type": "reply",
    "data": {
            "id": "5hit&d"
            "owner": "eurus"
            "messages": "Wow is awesome!"       
        }
}   

or

{
    "status": 200,
    "type": "thread",
    "data": {
            "id": "5hit8d",
            "owner": "eurus",
            "title": "DWM rice",
            "messages": "this is my configuration of DWM",      
        }
}    

```

##  status [message#]
Server send the status af a given message (sent/published)  
REQUEST  
> GET /messages/:id/status  

HEADER REQUEST 
```yaml
{
    "username": "eurus",
    "token": "bdsakjldj*ghdsjal"
}   
```


RESPONSE
```yaml
{
    "status": 200
    "data": "sent" | "published"
}    
```

## reply [message#]

Reply to a thread.  
REQUEST 
> POST /:topic/threads/:id    

where topic is the **:topic** name and **:id** is the message id is the id of the message to which the user must reply.  

HEADER REQUEST 
```yaml
{
    "username": "eurus",
    "token": "bdsakjldj*ghdsjal"
}   
```

BODY REQUEST 
```yaml
{
  "owner": "eurus",
  "message": "Wow is awesome!",     
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

HEADER REQUEST 
```yaml
{
    "username": "eurus",
    "token": "bdsakjldj*ghdsjal"
}   
```

BODY REQUEST 
```yaml
{
  "owner": "eurus",
  "title": "title test",
  "messages": "this is a new test thread" ,      
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
    "username": "eurus",
    "token": "bdsakjldj*ghdsjal"
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

HEADER REQUEST 
```yaml
{
    "username": "eurus",
    "token": "bdsakjldj*ghdsjal"
}   
```

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
400|BAD REQUEST
401|UNAUTHORIZED
404|NOT FOUND
408|REQUEST TIMEOUT
500|INTERNAL SERVER ERROR
