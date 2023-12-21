# Introduction to Cybersecurity Bootcamp
## 1. Introduction to CTF
## 2. Cybersecurity Overview
### CTFs
	- Mailer
	- Fisher
	- Competition
## 3. Attacks and Vulnrabilites
## 4. Data Encryption
## 5. Scapy
A tool build in python, enables network control, packets creation, sending over the network, and sniff packets.
- `PKT = IP()` create an ip packet
- `ls(..)` see atrributes
- `PKT.show()` see packet attributes
- `PKT.summary()` see packet summary
- `PKT = ICMP()` create an icmp packet for server to know what is it and how to reply to it.
- `DATA = Raw()` add the dta to it's load property.
- `sr1(IP/ICMP/DATA)` gets reponse from the server if it replied as a one packet.

## 6. Introduction to Kali Linux

## 7. Hash Cracking
First of all identify the hash, you can use a webiste like [Hash Analyzer](https://www.tunnelsup.com/hash-analyzer/)
- Webistes
	- crackstation.net
	- md5online.org
	- hashkiller.io
- Tools
	- John The Ripper
		- ex `john --format=Raw-SHA1 --wordlist=/usr/share/wordlists/rockyou.txt ./hash.txt`
	- Hashcat (Faster becuase it uses GPU)
		- ex: `hashcat -a 0 -m 3 f899139df5e1059396431415e770c6dd`
### Attacks
- Bruteforce
- Dictionary
	- ex: using Kali rockyou.txt password list `/usr/shar/wordlists/rockyou.txt`. 
	- A hash for each password is generated, and compared against the file hash.
- Rainbow table:
Uses a precalculated password hashed, compares them directly to the file hash.

