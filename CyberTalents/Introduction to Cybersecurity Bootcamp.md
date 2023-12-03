# Introduction to Cybersecurity Bootcamp
## CTF
### CTFs
	- Mailer
	- Fisher
	- Competition
## 2. Attacks and Vulnrabilites
## 3. Data Encryption
## 4. Scapy
A tool build in python, enables network control, packets creation, sending over the network, and sniff packets.
- `PKT = IP()` create an ip packet
- `ls(..)` see atrributes
- `PKT.show()` see packet attributes
- `PKT.summary()` see packet summary
- `PKT = ICMP()` create an icmp packet for server to know what is it and how to reply to it.
- `DATA = Raw()` add the dta to it's load property.
- `sr1(IP/ICMP/DATA)` gets reponse from the server if it replied as a one packet.
