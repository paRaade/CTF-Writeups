

Lab: [Crack the hash](https://tryhackme.com/room/crackthehash)
Tools: hashID, hashcat, John the Ripper, rockyou.txt, OneRuleToRuleThemAll.rule



All of these hashes can be cracked with the online tools:[crackstation](https://crackstation.net/) and [hashes.com](https://hashes.com/en/decrypt/hash).
![Pasted image 20240124204447](https://github.com/paRaade/CTF-Writeups/assets/126734769/f2bd69e8-19a5-4042-b796-b179e7ee4766)
![Pasted image 20240124215425](https://github.com/paRaade/CTF-Writeups/assets/126734769/ac272b71-67a6-4aa4-9dea-5bbe9c63d227)
![Pasted image 20240124211017](https://github.com/paRaade/CTF-Writeups/assets/126734769/7c1115c5-ba6d-46e9-a67e-9f9e294cd762)
![Pasted image 20240124215257](https://github.com/paRaade/CTF-Writeups/assets/126734769/9d73ff1d-7805-4049-8f4f-ee5679eac39d)


I still chose to use hashcat and john the ripper which are two offline password cracker tools for practice

 - Hashcat on Host OS (Windows)
 -  John the Ripper in Virtual Machine  (Linux)

**Level 1**

Hash: 48bb6e862e54f2a795ffc4e541caed4d

```
hashid 48bb6e862e54f2a795ffc4e541caed4d                                         
Analyzing '48bb6e862e54f2a795ffc4e541caed4d'
[+] MD2 
[+] MD5 
[+] MD4 
[+] Double MD5 
[+] LM 
[+] RIPEMD-128 
[+] Haval-128 
[+] Tiger-128 
[+] Skein-256(128) 
[+] Skein-512(128) 
[+] Lotus Notes/Domino 5 
[+] Skype 
[+] Snefru-128 
[+] NTLM 
[+] Domain Cached Credentials 
[+] Domain Cached Credentials 2 
[+] DNSSEC(NSEC3) 
[+] RAdmin v2.x 

hashcat -m 0 hahahashes.txt rockyou.txt -r rules/OneRuleToRuleThemAll.rule
48bb6e862e54f2a795ffc4e541caed4d:easy
```
--------
Hash: CBFDAC6008F9CAB4083784CBD1874F76618D2A97
```
hashid CBFDAC6008F9CAB4083784CBD1874F76618D2A97 
Analyzing 'CBFDAC6008F9CAB4083784CBD1874F76618D2A97'
[+] SHA-1 
[+] Double SHA-1 
[+] RIPEMD-160 
[+] Haval-160 
[+] Tiger-160 
[+] HAS-160 
[+] LinkedIn 
[+] Skein-256(160) 
[+] Skein-512(160) 

john -format:RAW-SHA1 -wordlist:rockyou.txt hashes.txt
password123      (?)     

john --show hashes.txt                                   
?:password123
```
-----
Hash: 1C8BFE8F801D79745C4631D09FFF36C82AA37FC4CCE4FC946683D7B336B63032
```
sha256
hashid 1C8BFE8F801D79745C4631D09FFF36C82AA37FC4CCE4FC946683D7B336B63032
Analyzing '1C8BFE8F801D79745C4631D09FFF36C82AA37FC4CCE4FC946683D7B336B63032'
[+] Snefru-256 
[+] SHA-256 
[+] RIPEMD-256 
[+] Haval-256 
[+] GOST R 34.11-94 
[+] GOST CryptoPro S-Box 
[+] SHA3-256 
[+] Skein-256 
[+] Skein-512(256) 

hashcat -m 1400 hahahashes.txt rockyou.txt -r rules/OneRuleToRuleThemAll.rule
1c8bfe8f801d79745c4631d09fff36c82aa37fc4cce4fc946683d7b336b63032:letmein
```
------

Hash: $2y$12$Dwt1BZj6pcyc3Dy1FWZ5ieeUznr71EeNkJkUlypTsgbX1H68wsRom


```
This one has a hint that is essential because of how long it takes to crack bcrypt hashes

hashid '$2y$12$Dwt1BZj6pcyc3Dy1FWZ5ieeUznr71EeNkJkUlypTsgbX1H68wsRom' 
Analyzing '$2y$12$Dwt1BZj6pcyc3Dy1FWZ5ieeUznr71EeNkJkUlypTsgbX1H68wsRom'
[+] Blowfish(OpenBSD) 
[+] Woltlab Burning Board 4.x 
[+] bcrypt 

cat rockyou.txt | wc -l
14344392

cp rockyou.txt rockyou_new.txt
awk 'length($0) <= 4' rockyou_new.txt > rockyou_four.txt

cat rockyou_four.txt | wc -l
21028

john --format=bcrypt --wordlist=rockyou_four.txt hashes.txt 
bleh             (?)     

john --show hashes.txt                                              
?:bleh
```
Hash: 279412f945939ba78ce0758d3fd83daa
```
MD4 Hash
hashid 279412f945939ba78ce0758d3fd83daa                              
Analyzing '279412f945939ba78ce0758d3fd83daa'
[+] MD2 
[+] MD5 
[+] MD4 
[+] Double MD5 
[+] LM 
[+] RIPEMD-128 
[+] Haval-128 
[+] Tiger-128 
[+] Skein-256(128) 
[+] Skein-512(128) 
[+] Lotus Notes/Domino 5 
[+] Skype 
[+] Snefru-128 
[+] NTLM 
[+] Domain Cached Credentials 
[+] Domain Cached Credentials 2 
[+] DNSSEC(NSEC3) 
[+] RAdmin v2.x 

hashcat -m 900 hahahashes.txt rockyou.txt -r rules/OneRuleToRuleThemAll.rule
279412f945939ba78ce0758d3fd83daa:Eternity22
```
**Level 2**
Hash: F09EDCB1FCEFC6DFB23DC3505A882655FF77375ED8AA2D1C13F640FCCC2D0C85
```
Sha256
hashid F09EDCB1FCEFC6DFB23DC3505A882655FF77375ED8AA2D1C13F640FCCC2D0C85
Analyzing 'F09EDCB1FCEFC6DFB23DC3505A882655FF77375ED8AA2D1C13F640FCCC2D0C85'
[+] Snefru-256 
[+] SHA-256 
[+] RIPEMD-256 
[+] Haval-256 
[+] GOST R 34.11-94 
[+] GOST CryptoPro S-Box 
[+] SHA3-256 
[+] Skein-256 
[+] Skein-512(256) 

hashcat -m 1400 hahahashes.txt rockyou.txt -r rules/OneRuleToRuleThemAll.rule
f09edcb1fcefc6dfb23dc3505a882655ff77375ed8aa2d1c13f640fccc2d0c85:paule
```


Hash: 1DFECA0C002AE40B8619ECF94819CC1B
```
NTLM
hashid 1DFECA0C002AE40B8619ECF94819CC1B                                
Analyzing '1DFECA0C002AE40B8619ECF94819CC1B'
[+] MD2 
[+] MD5 
[+] MD4 
[+] Double MD5 
[+] LM 
[+] RIPEMD-128 
[+] Haval-128 
[+] Tiger-128 
[+] Skein-256(128) 
[+] Skein-512(128) 
[+] Lotus Notes/Domino 5 
[+] Skype 
[+] Snefru-128 
[+] NTLM 
[+] Domain Cached Credentials 
[+] Domain Cached Credentials 2 
[+] DNSSEC(NSEC3) 
[+] RAdmin v2.x 

hashcat -m 1000 hahahashes.txt rockyou.txt -r rules/OneRuleToRuleThemAll.rule
1dfeca0c002ae40b8619ecf94819cc1b:n63umy8lkf4i
```
---------
```
Hash: $6$aReallyHardSalt$6WKUTqzq.UQQmrm0p/T7MPpMbGNnzXPMAXi4bJMl9be.cfi3/qxIf.hsGpS41BqMhSrHVXgMpdjS6xeKZAs02.

Salt: aReallyHardSalt
```
```
SHA-512

hashid '$6$aReallyHardSalt$6WKUTqzq.UQQmrm0p/T7MPpMbGNnzXPMAXi4bJMl9be.cfi3/qxIf.hsGpS41BqMhSrHVXgMpdjS6xeKZAs02.'
Analyzing '$6$aReallyHardSalt$6WKUTqzq.UQQmrm0p/T7MPpMbGNnzXPMAXi4bJMl9be.cfi3/qxIf.hsGpS41BqMhSrHVXgMpdjS6xeKZAs02.'
[+] SHA-512 Crypt 

hashcat -m 1800 hahahashes.txt rockyou.txt -O
$6$aReallyHardSalt$6WKUTqzq.UQQmrm0p/T7MPpMbGNnzXPMAXi4bJMl9be.cfi3/qxIf.hsGpS41BqMhSrHVXgMpdjS6xeKZAs02.:waka99
```

e5d8870e5bdd26602cab8dbe07a942c8669e56d6


```
HMAC-SHA1

hashid e5d8870e5bdd26602cab8dbe07a942c8669e56d6                                                                   
Analyzing 'e5d8870e5bdd26602cab8dbe07a942c8669e56d6'
[+] SHA-1 
[+] Double SHA-1 
[+] RIPEMD-160 
[+] Haval-160 
[+] Tiger-160 
[+] HAS-160 
[+] LinkedIn 
[+] Skein-256(160) 
[+] Skein-512(160) 

```
