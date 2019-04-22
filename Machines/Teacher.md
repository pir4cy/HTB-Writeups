# TEACHER
## Info

IP Address: 10.10.10.153  
OS: Linux  
Difficulty: Easy/Medium  

## System Enumeration

### NMAP
Using `nmap`, we check for open ports to access  
![Nmap](boxImages/Teacher/nmap.png "nmap")

Only `Port 80` is up.

### Dirbuster
To enumerate the website we use, `dirbuster`  
![Dirbuster](boxImages/Teacher/dirbuster.png "dirbuster")

### Website

![Website Front](boxImages/Teacher/websitefront.png "Website")

Just browsing the website we see a peculiar snippet of code on the `gallery.html` page  
![Source Code](boxImages/Teacher/sourcecode.png "Doubt")

Checking the console we find what we expected  
![F](boxImages/Teacher/f.png "That's an F")

To properly check everything on the website I decided to download the entire website to my local machine using `wget --mirror`  
![Copied](boxImages/Teacher/websitecopied.png "Copied website")

The above output in console tells me that there's something peculiar about `5.png`, let's check it out  
![strings 5](boxImages/Teacher/strings5.png "Strings")

#### Moodle

![Moodle](boxImages/Teacher/moodle.png "Moodle")

Seems like, this would be our way in. A quick google search `moodle exploit` shows the existence of an `Evil Teacher` exploit  
`https://blog.ripstech.com/2018/moodle-remote-code-execution/`

But it needs us to login to the service as a teacher.

### Using Hydra

From 5.png we have found 14/15 characters for the password  
`Th4C00lTheacha`  
To guess the last character for the password, we will use hydra  
I created a wordlist with a list of all characters and used that for hydra input  
```
hydra -l giovanni -P wordlist.txt 10.10.10.153 http-post-form "/moodle/login/index.php:username=^USER^&password=Th4C00lTheacha^PASS^:Invalid login, please try again" -Vv
```
We found the password and logged in to moodle

### Remote Code Execution

Step 1: Create a quiz.  
Step 2: Add a calculated question.
	
	![Adding a Question](boxImages/Teacher/questionadd.png "Calculated Question")  
Step 3: Add the formula given to the answer.
	
	![Answer](boxImages/Teacher/formulaUsed.png "Formula Used")  
Step 4: Use the RCE to obtain a reverse shell.  
	I used the netcat reverse shell, simply append the code like so

	```
	&0=(<your code here>)
	```

![RevShell](boxImages/Teacher/revshell.png "RevShell")

## User Elevation

Using `ps aux` we find that we have a MySQL service running in the system.  
![Ps Aux](boxImages/Teacher/psaux.png "ps aux")

Enumerating the current directory  
![Enumeration](boxImages/Teacher/accessibledata.png "LS")

Let's checkout `config.php`  
![Config.php](boxImages/Teacher/configphp.png "Config.php")

As we can see, the credentials are given in plaintext, so we can login

### MariaDB

Logging into the service using `mysql -u root -p` and using the password we obtained  
![MariaDB login](boxImages/Teacher/mariadblogin.png "Logged In")

Checking the databases we can operate with using `show databases;`  
![Databases](boxImages/Teacher/databases.png "Databases")

The database `moodle` seems like a nice place to start, `use moodle` to access moodle  
Using `show tables` to see all tables in this database, we find the table `user`  
Since config.php stated that the prefix used is `mdl_`, we will work accordingly.  
`select * from mdl_user` gives us  
![unorganised output](boxImages/Teacher/selectstar.png "Unreadable")

Let's try to make it readable using `select id,auth,username,password from mdl_user;`  
![Password](boxImages/Teacher/passdiscovers.png "Password")

Cracking the hash we see for `Giovannibak` we get a password, hopefully giovanni hasn't changed the password and it's the same as the backup.  

### Logging as Giovanni

Using `su -- giovanni` we try to elevate from `www` to `giovanni`  
![su](boxImages/Teacher/tryingSU.png "su")

## User Gained

![User](boxImages/Teacher/usergained.png "User")

## Going for Root

In our current working directory we see a work folder, in which there's a tmp folder.  

In this directory the `courses` from the work folder are being backed up.  
So to get `root.txt`, we attempt to create a symlink to the `/root/` directory.  
![Exploiting Backup](boxImages/Teacher/root.png "Root.txt Gained")
