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

 ## list topic sub 
 The server send all the topics to which the user is subscribed.  
 REQUEST
 > GET /topics/sub

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
        "unixconfig", 
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

## list [messages of topics] 
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
     "topic": "unixconfig",
     "owner": "eurus",
     "thread_id": [
        "di%s!o",
        "5j*dkp",
        "h&tsk#",
        "l8t7rb",
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

## reply [message#]

Reply to a thread.  
REQUEST 
> POST /replies/:id    

where **:id** is the message id is the id of the message to which the user must reply.  

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
  "message": "Wow is awesome!",     
}    
```
RESPONSE
```yaml
{
    "status": 200
}    
```

## append [topic | thread]
Create a new thread in a topic  
REQUEST  
> POST /threads/:topicname  

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
  "title": "title test",
  "message": "this is a new test thread" ,      
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
>POST /topics/:topicname  

RESPONSE
```yaml
{
    "status": 200
}    
```

## delete [topic]
Delete a topic only if the user is the topic's owner  
REQUEST 
> DELETE /topics/:topicname 

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

## subscribe [topic]
Subscribe a user to a topic  
REQUEST  
> POST /topics/:topicname/sub

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

## Register [user]
register new user  
REQUEST  
> POST /user

HEADER REQUEST 
```yaml
{
    "username": "eurus",
    "passwordhash": "hashhashhash"
}   
```

RESPONSE
```yaml
{
    "status": 200,
    token": "bdsakjldj*ghdsjal"
}    
```

## Login [user]
login user  
REQUEST  
> GET /user/login

HEADER REQUEST 
```yaml
{
    "username": "eurus",
    "passwordhash": "hashhashhash"
}   
```

RESPONSE
```yaml
{
    "status": 200,
    token": "bdsakjldj*ghdsjal"
}   
```

## Logout [user]
logout user  
REQUEST  
> GET /user/logout

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
