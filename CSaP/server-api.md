 # API server CSap 2019-2020 (whiteboard)

 ## command: authenticate
 **TODO**

 ## list messages
 REQUEST
 > GET /messages/:userid-or-token  
 ---
RESPONSE
```yaml
{
    "status": 200
    "lenght": 2
    "data": [
        {
            "id": "123e4567-e89b-12d3-a456-426614174000"
            "thread": "unixconfig" 
            "from": "eurus"
            "to": "isfet"
            "text": "Wow is awesome!"       
        },
        {
            "id": "123e4567-e89b-12d3-a456-426614174000"
            "thread": "unixconfig"  
            "from": "isfet"
            "to": "eurus"
            "when": "2012-04-21T18:25:43-05:00" 
            "text": "This is my configuration of DWM"       
        },  
    ]
}  
```
## list topics
REQUEST  
> GET /topics/:userid-or-token  

RESPONSE
```yaml
{
    "status": 200
    "lenght": 2
    "data": [
        "unixconfig",
        "cyberpunk" 
    ]
}    
