## Info
  * IP: 10.10.10.150
  * OS: Linux
  * Difficulty: Easy

## System Enumeration

### Nmap

![Nmap](boxImages/Curling/nmap.png "Nmap")

### Website

![Website](boxImages/Curling/website.png "Website Main Page")

#### Source Code

Looking at the source code of the main page, we find `<!-- secret.txt -->`
![Source Code](boxImages/Curling/sourceCode.png "Source Code")

So, we look at `10.10.10.150/secret.txt` which gives us `Q3VybGluZzIwMTgh`.  
This code seems like base64, decoding it, we get: `Curling2018!`  

#### Logging In To Website

Looking at some of the posts on the website, we see a name `Floris`.  
Let us try logging in with  
```
    username: Floris
    password: Curling2018!
```

![LoggedIn](boxImages/Curling/loggedIn.png "Logged In Main Page")

#### /administrator/ - Joomla Interface

Trying the same credentials for Joomla interface
![Joomla Interface](boxImages/Curling/joomlaInterface.png "Joomla Interface")

And we are in: 

![Joomla Accessed](boxImages/Curling/joomlaAccess.png "Logged In Joomla")

### Exploiting Joomla

After logging in, we try to exploit the Joomla Interface

Step 1: Go to Extensions -> Templates
    ![Templates](boxImages/Curling/template1.png "Templates")
Step 2: Go to Templates -> Options
    ![Templates->Options](boxImages/Curling/template2.png "Templates -> Options")
Step 3: Set Public and Super User permissions to be allowed
    ![Permissions](boxImages/Curling/permissions.png "Permissions")
    Save and close.  
Step 4: Click on Templates and open Beez3 Details
    ![Templates->Templates](boxImages/Curling/templateBeez3.png "Templates Details")
    Edit `index.php`    
    ![Beez3 Details](boxImages/Curling/beez3.png "Beez3 Details")
Step 5: Replace existing code with `php-reverse-shell` code and save.


Now we start a listener on our local machine using `nc -lvnp 1234`.  

Click on template preview after saving the file, and we obtain a shell.  

![Shell Obtained](boxImages/Curling/shellObtained.png "Shell Obtained")

We cannot view `user.txt` or any user data because we are `www-data`, so we will have to escalate our privileges to user `floris`

![WWW-Data](boxImages/Curling/wwwData.png "wwwdata")

We find a peculiar looking file called `password_backup`.  
To check it out properly we should download it to our local machine.  

Once, again, I used base64 conversions to download the file to my system.  

![Base64 Conversion](boxImages/Curling/base64Conv.png "Base 64 Conversion")

![Base64 Decode](boxImages/Curling/base64Dec.png "Base 64 Decode")

And we have our file

#### password_backup

`file password_backup` shows that it is an ASCII text file.  

`less password_backup` 
![Less Output](boxImages/Curling/lessOutput.png "Less Password_Backup")

So we see that it is a hexdump file.  
To completely decode the given file we use a collection of compression tools and finally get our password.  

![password_backup](boxImages/Curling/passwordBackup.png "Cracking password_backup")

We can directly try this password using `su --floris`, but it didn't work

![su fail](boxImages/Curling/suFail.png "su fail")

Let's try this for `ssh`

### SSH

Logging in `ssh floris@10.10.10.150` and password obtained from `password_backup` we are in:

![SSH](boxImages/Curling/sshLogin.png "SSH Logged In")

And we get `user.txt`

![User Exposed](boxImages/Curling/userExposed.png "User Exposed")

## Root Exposed

Traversing to the `admin-area` we see that there are 2 files.  
  * input
  * report

Opening them up, we can see the contents of both files:

  * input
    ![input file](boxImages/Curling/inputFile.png "Input File")
  * report
    ![input file](boxImages/Curling/reportFile.png "Report File")


Now, there are 2 ways to get the contents of `root.txt`.  

Both files keep changing every minute, which suggests that there is a cronjob running which keeps updating the files every minute.  
ly copy it
To read cronjobs we use `pspy` (src = https://github.com/DominicBreuker/pspy). To use pspy, we simply copy the pspy binary to the host machine and run it.  

#### PSPY OUTPUT
![PSPY Output](boxImages/Curling/pspyOutput.png "PSPY Output")

We can see that the cronjob is calling `curl -K` and takes the `input` file as input and outputs into the `report` file.  

There are 2 ways to get the contents of `root.txt`.  

### Way 1

Changing the contents of input to `url = file:///root/root.txt`.

![Way One of getting root](boxImages/Curling/root1.png "Way One of getting root")

### Way 2

Since `curl -K` accepts configuration files as input, we will exploit the input file and escalate the user `floris` to root.  

Step 1: Create `sudoers.txt` on your local machine, with the following text  
    `floris ALL = (ALL:ALL) ALL`
Step 2: Create listener on your local machine using python  
    `python -m SimpleHTTPServer`
Step 3: Edit the input file, like so  
    ![input File root](boxImages/Curling/inputRoot2.png "Input File for Way 2")
Step 4: Wait for the cronjob to execute, which will download `sudoers.txt` to `/etc/sudoers`
    ![sudoers updated](boxImages/Curling/sudoers.png "Sudoers Updated")
Step 5: Run `sudo su`
    ![Root Gained](boxImages/Curling/root2.png "Way 2 of getting root")
