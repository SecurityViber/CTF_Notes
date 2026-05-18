---
tags: [cdsa, htb, incident-response]
---

[[Certifications/CDSA/Index|← CDSA]]





The 7 stages of the cyberkillchain
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

Four stages of the Incident Handling Process
	![[nist_incident_handling_process.png]]
	Preparation phase
		**Clear Policies & Documentation**	
			- Contact information and roles of the incident handling team members
			- Contact information for the legal and compliance department, management team, IT support, communications and media relations department, law enforcement, internet service providers, facility management, and external incident response team
			- Incident response policy, plan, and procedures
			- Incident information sharing policy and procedures
			- Baselines of systems and networks, out of a golden image and a clean state environment
			- Network diagrams
			- Organization-wide asset management database
			- User accounts with excessive privileges that can be used on-demand by the team when necessary (also to business-critical systems, which are handled with the skills needed to administer that specific system). These user accounts are normally enabled when an incident is confirmed during the initial investigation and then disabled once it is over. A mandatory password 
			- reset is also performed when disabling the users.
			- Ability to acquire hardware, software, or an external resource without a complete procurement process (urgent purchase of up to a certain amount). The last thing you need during an incident is to wait for weeks for the approval of a $500 tool.
			- Forensic/Investigative cheat sheets
		**Jump Bag Equipment**
			- Additional laptop or a forensic workstation for each incident handling team member to preserve disk images and log files, perform data analysis, and investigate without any restrictions (we know malware will be tested here, so tools such as antivirus should be disabled). These devices should be handled appropriately and not in a way that introduces risks to the organization.
			- Digital forensic image acquisition and analysis tools
			- Memory capture and analysis tools
			- Live response capture and analysis
			- Log analysis tools
			- Network capture and analysis tools
			- Network cables and switches
			- Write blockers
			- Hard drives for forensic imaging
			- Power cables
			- Screwdrivers, tweezers, and other relevant tools to repair or disassemble hardware devices if needed
			- Indicator of Compromise (IOC) creator and the ability to search for IOCs across the organization
			- Chain of custody forms
			- Encryption software
			- Ticket tracking system
			- Secure facility for storage and investigation
			- Incident handling system independent of your organization's infrastructure
		Prepare against Incidents with things such as
			- Endpoint Hardening
			- Network Protection
			- Privilege Identity Management
			- MFA
			- Passwords
			- Perform continuous vulnerability scans of your environment
			- User Awareness Training
			- Active Directory Security Assessment
	Detection & Analysis
		Questions to answer
			- What is the exploitation impact?
			- What are the exploitation requirements?
			- Can any business-critical systems be affected by the incident?
			- Are there any suggested remediation steps?
			- How many systems have been impacted?
			- Is the exploit being used in the wild?
			- Does the exploit have any worm-like capabilities?

The Investigation Process
	![[investigation_process.png]]

Containment, Eradication & Recovery Stage
	1. First step is the containment. This includes elements such as, modifying C2 DNS names, rotating passwords, pulling network cables etc.
	2. Eradication is done after the containment. Goal is to remove the root cause(such as malware)
	3. Recovery stage, which can take months 

Post-Incident Activity Stage
	- Write final Report
		- What happened and when?
		- Performance of the team dealing with the incident in regard to plans, playbooks, policies, and procedures
		- Did the business provide the necessary information and respond promptly to aid in handling the incident in an efficient manner? What can be improved?
		- What actions have been implemented to contain and eradicate the incident?
		- What preventive measures should be put in place to prevent similar incidents in the future?
		- What tools and resources are needed to detect and analyze similar incidents in the future?

