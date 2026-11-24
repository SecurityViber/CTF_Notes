---
tags: [cdsa, htb, incident-response]
---

> [!info] See also: [[Cyberkill Chain]] · [[SOC and SIEM]] · [[Windows EventLogs]] · [[0 Links and Resources|Links and Resources]]


## Incident Handling Process (NIST)

The four stages of the incident handling process:

![[nist_incident_handling_process.png]]

1. Preparation
2. Detection & Analysis
3. Containment, Eradication & Recovery
4. Post-Incident Activity


### 1. Preparation

**Clear policies & documentation**

- Contact information and roles of the incident handling team members
- Contact information for: legal and compliance, management, IT support, communications and media relations, law enforcement, internet service providers, facility management, external incident response team
- Incident response policy, plan, and procedures
- Incident information sharing policy and procedures
- Baselines of systems and networks, from a golden image and a clean-state environment
- Network diagrams
- Organization-wide asset management database
- On-demand user accounts with excessive privileges for the team (including business-critical systems)
  - Enabled when an incident is confirmed during the initial investigation, disabled once it is over
  - Mandatory password reset when disabling them
- Ability to acquire hardware, software, or external resources without the full procurement process (urgent purchases up to a set amount). You don't want to wait weeks for approval of a $500 tool during an incident.
- Forensic/investigative cheat sheets

**Jump bag equipment**

- Additional laptop or forensic workstation per team member to preserve disk images and log files, analyze data, and investigate without restrictions
  - Malware will be tested here, so AV is disabled. Handle these devices so they don't introduce risk to the organization.
- Digital forensic image acquisition and analysis tools
- Memory capture and analysis tools
- Live response capture and analysis tools
- Log analysis tools
- Network capture and analysis tools
- Network cables and switches
- Write blockers
- Hard drives for forensic imaging
- Power cables
- Screwdrivers, tweezers, and other tools to repair or disassemble hardware
- Indicator of Compromise (IOC) creator and the ability to search for IOCs across the organization
- Chain of custody forms
- Encryption software
- Ticket tracking system
- Secure facility for storage and investigation
- Incident handling system independent of the organization's infrastructure

**Protective measures against incidents**

- Endpoint hardening
- Network protection
- Privileged identity management
- MFA
- Password policies
- Continuous vulnerability scans of the environment
- User awareness training
- Active Directory security assessment


### 2. Detection & Analysis

Questions to answer:

- What is the exploitation impact?
- What are the exploitation requirements?
- Can any business-critical systems be affected by the incident?
- Are there any suggested remediation steps?
- How many systems have been impacted?
- Is the exploit being used in the wild?
- Does the exploit have any worm-like capabilities?

#### The Investigation Process

![[investigation_process.png]]


### 3. Containment, Eradication & Recovery

1. **Containment**: stop the spread, e.g. modifying C2 DNS names, rotating passwords, pulling network cables
2. **Eradication**: done after containment; remove the root cause (e.g. the malware)
3. **Recovery**: bring systems back to normal operation (can take months)


### 4. Post-Incident Activity

The workflow once an incident is resolved:

![[post_incident_workflow.png]]

1. Incident Resolved
2. Post-Incident Review
3. Lessons Learned Meeting
4. Root Cause Analysis
5. Update Policies and Playbooks
6. Enhance Detection & Monitoring Rules
7. Knowledge Sharing & Awareness
8. Report to Management & Close Case

Write the final report, covering:

- What happened and when?
- How did the team perform against plans, playbooks, policies, and procedures?
- Did the business provide the necessary information and respond promptly? What can be improved?
- What actions were implemented to contain and eradicate the incident?
- What preventive measures should be put in place to prevent similar incidents?
- What tools and resources are needed to detect and analyze similar incidents in the future?
