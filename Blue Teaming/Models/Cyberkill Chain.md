
![[cyberkillchain.png]]
		Recon
			-  Initial stage
			- Attacker chooses target + performs information gathering (osint etc.)
		Weaponize
			- Malware for initial accces is developed and embedded into type of exploit
			- Malware is lightweight and should not be detected by AV or EDR
			- Goal is to provide a persistent Remote Access
		Deliver
			- Exploit/Payload gets delivered to the victim.
			- Usually phishing via email/phone
		Exploitation stage
			- Moment when exploit or delivered payload is triggered
			- attacker attempts to execute code on the target system in order to gain access or control
		Installation stage
			- Initial stager is executed and running on compromised machine
			- Typical techniques are: Droppers, Backdoors, Rootkits
		Command and Control stage
			- Attacker establishes a remote access capability to compromised machine
		Action
			- Can be data exfiltration, gaining higher level of privileges etc.
			- 