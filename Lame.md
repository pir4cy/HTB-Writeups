# LAME

## Info

IP Address: 10.10.10.3  
OS: Linux  
Difficulty: Easy  

## System Enumeration

### NMAP

![alt text](https://github.com/Gesundheit/HTB-Writeups/blob/master/boxImages/Lame/nmap.png "NMAP")

We see that we have 4 open ports:  

  * 21 - FTP    
  * 22 - SSH  
  * 139 - Samba 3.X - 4.X  
  * 445 - Samba 3.0.20 - Debian  
  
#### FTP Exploit 

First thing that came to mind was logging in to the FTP, but that wasn't fruitful so I ended up searching for VSFTPD 2.3.4 and see if it had any possible vulnerabilities I could exploit and 
sure enough we had a backdoor exploit that could be used to get in.  
But it failed:

![alt text](https://github.com/Gesundheit/HTB-Writeups/blob/master/boxImages/Lame/ftp.png "FTP")

So we move on to the other exploitable service I could see: `Samba`

#### Samba Exploit

A quick google of `Samba 3.0.20 - Debian` shows

![alt text](https://github.com/Gesundheit/HTB-Writeups/blob/master/boxImages/Lame/googlesamba.png "Google Result")

The first link directly gives us everything we need to pop this box

![alt text](https://github.com/Gesundheit/HTB-Writeups/blob/master/boxImages/Lame/rapidpage.png "Rapid7")

## Exploit

A simple and straightforward exploit.  
  * set RHOST `10.10.10.3`
  * run
  
![alt text](https://github.com/Gesundheit/HTB-Writeups/blob/master/boxImages/Lame/msfconsole.png "MSFConsole")

Finally, we obtain a root shell and hence our flags

![alt text](https://github.com/Gesundheit/HTB-Writeups/blob/master/boxImages/Lame/owned.png "Owned")
