

**Lab**:
 [Log Analysis – Compromised WordPress](https://blueteamlabs.online/home/challenge/log-analysis-compromised-wordpress-ce000f5b59)

**Scenario**:
One of our WordPress sites has been compromised but we're currently unsure how. The primary hypothesis is that an installed plugin was vulnerable to a remote code execution vulnerability which gave an attacker access to the underlying operating system of the server.

**Incident Timeframe:**
January 12, 2021, at 15:52:41 (3:52:41 PM) UTC to  January 14, 2021, at 07:46:52 (7:46:52 AM) UTC


**Tools used**: 
Visual Studio Code,  grep

**Summary:**
Following the logic of the hypothesis in the scenario. The reasonable thing to look for in the access.log provided is to look for the WordPress login portal. Typically when exploiting remote code execution vulnerabilities in WordPress it requires the attacker to first have an authenticated session.

A quick google search shows that WordPress login file name is: wp-login.php.

image of google

Using  **grep -n "wp-login.php" access.log** I can see multiple URL  requests to **/wp-login.php?itsec-hb-token=adminlogin**. 
```
68:172.21.0.1 - - [12/Jan/2021:15:53:22 +0000] "GET /wp-login.php?redirect_to=http%3A%2F%2F172.21.0.3%2Fwp-admin%2F&reauth=1 HTTP/1.1" 200 4633 "-" "Mozilla/5.0 (X11; Linux x86_64; rv:78.0) Gecko/20100101 Firefox/78.0"
69:172.21.0.1 - - [12/Jan/2021:15:53:22 +0000] "GET /wp-includes/css/buttons.min.css?ver=5.6 HTTP/1.1" 200 1788 "http://172.21.0.3/wp-login.php?redirect_to=http%3A%2F%2F172.21.0.3%2Fwp-admin%2F&reauth=1" "Mozilla/5.0 (X11; Linux x86_64; rv:78.0) Gecko/20100101 Firefox/78.0"
70:172.21.0.1 - - [12/Jan/2021:15:53:22 +0000] "GET /wp-admin/css/login.min.css?ver=5.6 HTTP/1.1" 200 2296 "http://172.21.0.3/wp-login.php?redirect_to=http%3A%2F%2F172.21.0.3%2Fwp-admin%2F&reauth=1" "Mozilla/5.0 (X11; Linux x86_64; rv:78.0) Gecko/20100101 Firefox/78.0"
71:172.21.0.1 - - [12/Jan/2021:15:53:22 +0000] "GET /wp-includes/js/zxcvbn-async.min.js?ver=1.0 HTTP/1.1" 200 608 "http://172.21.0.3/wp-login.php?redirect_to=http%3A%2F%2F172.21.0.3%2Fwp-admin%2F&reauth=1" "Mozilla/5.0 (X11; Linux x86_64; rv:78.0) Gecko/20100101 Firefox/78.0"
72:172.21.0.1 - - [12/Jan/2021:15:53:22 +0000] "GET /wp-admin/css/forms.min.css?ver=5.6 HTTP/1.1" 200 6469 "http://172.21.0.3/wp-login.php?redirect_to=http%3A%2F%2F172.21.0.3%2Fwp-admin%2F&reauth=1" "Mozilla/5.0 (X11; Linux x86_64; rv:78.0) Gecko/20100101 Firefox/78.0"

```
Total wp-login.php count in access.log: **grep -n "wp-login.php" access.log | wc -l**  `225 `

I then looked up a list of  web tools used by attackers in their enumeration or exploitation process.
**grep -n -e "wpscan" -e "dirbuster" -e "dirsearch" -e "nmap" -e "Burp" -e "ZAP" -e "metasploit" -e "sqlmap" access.log** 
```
1364:119.241.22.121 - - [14/Jan/2021:06:01:41 +0000] "GET / HTTP/1.1" 403 3160 "http://172.21.0.3/" "WPScan v3.8.10 (https://wpscan.org/)"
1528:168.22.54.119 - - [14/Jan/2021:06:12:53 +0000] "POST /wp-login.php HTTP/1.1" 302 243 "-" "sqlmap/1.4.11#stable (http://sqlmap.org)"
```

#### *January 14, 2021, at 06:01:41 (UTC)*
The attacker used wpscan. Wpscan is a WordPress vulnerability scanner that can be used for user enumeration and vulnerability scanning.

####  *January 14, 2021, at 06:12:53 (UTC)* 
The attacker used sqlmap. SQLMap is an open-source penetration testing tool that automates the process of detecting and exploiting SQL injection vulnerabilities in web applications

#### *January 14, 2021, at 06:14:00 (UTC)* 

A blended attack attempt is first performed

**grep -n "xp_cmdshell" access.log**         
```
534:168.22.54.119 - - [14/Jan/2021:06:14:00 +0000] "POST /wp-login.php?itsec-hb-token=adminlogin&mglS=2151%2BAND%2B1%3D1%2BUNION%2BALL%2BSELECT%2B1%2CNULL%2C%27%3Cscript%3Ealert%28%22XSS%22%29%3C%2Fscript%3E%27%2Ctable_name%2BFROM%2Binformation_schema.tables%2BWHERE%2B2%3E1--%2F%2A%2A%2F%3B%2BEXEC%2Bxp_cmdshell%28%27cat%2B..%2F..%2F..%2Fetc%2Fpasswd%27%29%23 HTTP/1.1" 200 2831 "-" "Mozilla/5.0 (X11; U; Linux i686 (x86_64); ru; rv:1.8.0.3) Gecko/20060425 SUSE/1.5.0.3-7 Firefox/1.5.0.3"
1539:168.22.54.119 - - [14/Jan/2021:06:14:00 +0000] "POST /wp-login.php?itsec-hb-token=p@y<\"'p@y&mglS=2151%2BAND%2B1%3D1%2BUNION%2BALL%2BSELECT%2B1%2CNULL%2C%27%3Cscript%3Ealert%28%22XSS%22%29%3C%2Fscript%3E%27%2Ctable_name%2BFROM%2Binformation_schema.tables%2BWHERE%2B2%3E1--%2F%2A%2A%2F%3B%2BEXEC%2Bxp_cmdshell%28%27cat%2B..%2F..%2F..%2Fetc%2Fpasswd%27%29%23 HTTP/1.1" 403 3252 "-" "Mozilla/5.0 (X11; U; Linux i686 (x86_64); ru; rv:1.8.0.3) Gecko/20060425 SUSE/1.5.0.3-7 Firefox/1.5.0.3"
1540:168.22.54.119 - - [14/Jan/2021:06:14:01 +0000] "POST /wp-login.php?itsec-hb-token=adminlogin&mglS=2151%2BAND%2B1%3D1%2BUNION%2BALL%2BSELECT%2B1%2CNULL%2C%27%3Cscript%3Ealert%28%22XSS%22%29%3C%2Fscript%3E%27%2Ctable_name%2BFROM%2Binformation_schema.tables%2BWHERE%2B2%3E1--%2F%2A%2A%2F%3B%2BEXEC%2Bxp_cmdshell%28%27cat%2B..%2F..%2F..%2Fetc%2Fpasswd%27%29%23 HTTP/1.1" 403 3303 "-" "Mozilla/5.0 (X11; U; Linux i686 (x86_64); ru; rv:1.8.0.3) Gecko/20060425 SUSE/1.5.0.3-7 Firefox/1.5.0.3"
1542:168.22.54.119 - - [14/Jan/2021:06:14:01 +0000] "POST /wp-login.php?itsec-hb-token=adminlogin&mglS=2151%2BAND%2B1%3D1%2BUNION%2BALL%2BSELECT%2B1%2CNULL%2C%27%3Cscript%3Ealert%28%22XSS%22%29%3C%2Fscript%3E%27%2Ctable_name%2BFROM%2Binformation_schema.tables%2BWHERE%2B2%3E1--%2F%2A%2A%2F%3B%2BEXEC%2Bxp_cmdshell%28%27cat%2B..%2F..%2F..%2Fetc%2Fpasswd%27%29%23 HTTP/1.1" 403 3303 "-" "Mozilla/5.0 (X11; U; Linux i686 (x86_64); ru; rv:1.8.0.3) Gecko/20060425 SUSE/1.5.0.3-7 Firefox/1.5.0.3"
1543:168.22.54.119 - - [14/Jan/2021:06:14:01 +0000] "POST /wp-login.php?itsec-hb-token=adminlogin&mglS=2151%2BAND%2B1%3D1%2BUNION%2BALL%2BSELECT%2B1%2CNULL%2C%27%3Cscript%3Ealert%28%22XSS%22%29%3C%2Fscript%3E%27%2Ctable_name%2BFROM%2Binformation_schema.tables%2BWHERE%2B2%3E1--%2F%2A%2A%2F%3B%2BEXEC%2Bxp_cmdshell%28%27cat%2B..%2F..%2F..%2Fetc%2Fpasswd%27%29%23 HTTP/1.1" 403 3303 "-" "Mozilla/5.0 (X11; U; Linux i686 (x86_64); ru; rv:1.8.0.3) Gecko/20060425 SUSE/1.5.0.3-7 Firefox/1.5.0.3"
```
**Breakdown of blended Attack Attempt**

Decoded URL: 
```
POST /wp-login.php?itsec-hb-token=adminlogin&mglS=2151+AND+1=1+UNION+ALL+SELECT+1,NULL,'<script>alert("XSS")</script>',table_name+FROM+information_schema.tables+WHERE+2>1--/**/;+EXEC+xp_cmdshell('cat+../../../etc/passwd')# HTTP/1.1
```
SQL Injection:
```
itsec-hb-token=adminlogin&mglS=2151+AND+1=1+UNION+ALL+SELECT+1,NULL,'<script>alert("XSS")</script>',table_name+FROM+information_schema.tables+WHERE+2>1--/**/;
```
Local File Inclusion:
``
EXEC+xp_cmdshell('cat+../../../etc/passwd')
``

XSS Payload:
```
<script>alert("XSS")</script>
```
#### *January 14, 2021, at 06:26:53 (UTC)*
After finding a POST request containing **ee-upload-engine.php** I used google to find any information about this and exploit-db was the top result. Exploit-DB, is a online platform that archives  information about security vulnerabilities and exploits. The link I clicked on was for a python script called. **WordPress Plugin Simple File List 4.2.2 - Arbitrary File Upload**. This Python script exploits an arbitrary file upload vulnerability in WordPress plugin 'Simple File List' (version 4.2.2). It generates a .png file with an embedded reverse shell payload, uploads it to the target WordPress site, and then renames the file extension to .php. Upon successful exploitation, the attacker gains remote code execution on the target system.

**grep -n "ee-upload-engine.php" access.log**     
```
1714:119.241.22.121 - - [14/Jan/2021:06:26:53 +0000] "POST /wp-content/plugins/simple-file-list/ee-upload-engine.php HTTP/1.1" 200 236 "-" "python-requests/2.24.0"
```
Further confirmation that this script was used by the attacker is that immediately after this POST request their is a GET request containing fr34k.png that in following Requests turns into fr34k.php

**grep -n "ee-upload-engine.php" access.log -A 5**
```
1714:119.241.22.121 - - [14/Jan/2021:06:26:53 +0000] "POST /wp-content/plugins/simple-file-list/ee-upload-engine.php HTTP/1.1" 200 236 "-" "python-requests/2.24.0"
1715-119.241.22.121 - - [14/Jan/2021:06:26:53 +0000] "GET /wp-content/uploads/simple-file-list/fr34k.png HTTP/1.1" 200 84690 "-" "python-requests/2.24.0"
1716-119.241.22.121 - - [14/Jan/2021:06:26:53 +0000] "POST /wp-content/plugins/simple-file-list/ee-file-engine.php HTTP/1.1" 200 236 "http://172.21.0.3/wp-admin/admin.php?page=ee-simple-file-list&tab=file_list&eeListID=1" "python-requests/2.24.0"
1717-103.69.55.212 - - [14/Jan/2021:06:27:04 +0000] "GET /wp-content/uploads/simple-file-list/fr34k.php HTTP/1.1" 200 1295 "-" "Mozilla/4.0 (compatible; MSIE 7.0; Windows NT 6.1; Trident/4.0; SLCC2; .NET CLR 2.0.50727; .NET CLR 3.5.30729; .NET CLR 3.0.30729; Media Center PC 6.0; Tablet PC 2.0)"
1718-sh: 1: /usr/sbin/sendmail: not found
1719-103.69.55.212 - - [14/Jan/2021:06:27:06 +0000] "POST /wp-content/uploads/simple-file-list/fr34k.php HTTP/1.1" 200 1213 "http://172.21.0.3/wp-content/uploads/simple-file-list/fr34k.php" "Mozilla/4.0 (compatible; MSIE 7.0; Windows NT 6.1; Trident/4.0; SLCC2; .NET CLR 2.0.50727; .NET CLR 3.5.30729; .NET CLR 3.0.30729; Media Center PC 6.0; Tablet PC 2.0)"
```
