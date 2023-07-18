---
title: "Controls Assessment"
date: 2023-07-17T20:52:21-07:00
categories: ["cybersecurity", "portfolio", "security audit"]
---
[Back to Security Audit]({{< ref "security-audit" >}})
# Controls Assessment

## Current assets
Assets managed by the IT Department include:
- On-premises equipment for in-office business needs
- Employee equipment: end-user devices (desktops/laptops, smartphones), remote workstations, headsets, cables, keyboards, mice, docking stations, surveillance cameras, etc.
- Management of systems, software, and services: accounting, telecommunication, database, security, ecommerce, and inventory management
- Internet access
- Internal network
- Vendor access management
- Data center hosting services
- Data retention and storage
- Badge readers
- Legacy system maintenance: end-of-life systems that require human monitoring

## Administrative Controls

Least Priviledge
: **Needs to be implemented; High**  
  Preventative; reduces risk by making sure vendors and non-authorized staff
  only have access to the assets/data they need to do their jobs

Disaster recovery plans
: **Needs to be implemented; Medium**  
  Corrective; business continuity to ensure systems are able to run in the event
  of an incident/there is limited to no loss of productivity downtime/impact to
  system components, including: computer room environment (air conditioning,
  power supply, etc.); hardware (servers, employee equipment); connectivity
  (internal network, wireless); applications (email, electronic data); data and
  restoration

Password policies
: **Needs to be implemented; Medium**  
  Preventative; establish password strength rules to improve security/reduce
  likelihood of account compromise through brute force or dictionary attack
  techniques

Access control policies
: **Needs to be implemented; High**  
  Preventative; increase confidentiality and integrity of data

Account management policies
: **Needs to be implemented; High**  
  Preventative; reduce attack surface and limit overall impact from disgruntled/former employees

Separation of duties
: **Needs to be implemented; High**  
  Preventative; ensure no one has so much access that they can abuse the system for personal gain

## Technical Controls
Firewall
: Preventative; firewalls are already in place to filter unwanted/malicious traffic from entering internal network

Intrusion Detection System (IDS)
: Detective; allows IT team to identify possible intrusions (e.g., anomalous traffic) quickly

Encryption
: **Needs to be implemented; Medium**  
  Deterrent; makes confidential information/data more secure (e.g., website payment transactions)
  
Backups
: **Needs to be implemented; Medium**  
  Corrective; supports ongoing productivity in the case of an event; aligns to the disaster recovery plan

Password management system
: **Needs to be implemented; Medium**  
  Corrective; password recovery, reset, lock out notifications

Antivirus (AV) software
: **Needs to be implemented; High**  
  Corrective; detect and quarantine known threats

Manual monitoring, maintenance, and intervention
: **Needs to be implemented; Medium**  
  Preventative/corrective; required for legacy systems to identify and mitigate potential threats, risks, and vulnerabilities

## Physical Controls

Time-controlled safe
: **Needs to be implemented; Low**  
  Deterrent; reduce attack surface/impact of physical threats

Adequate lighting
: **Needs to be implemented; Low**  
  Deterrent; limit “hiding” places to deter threats

Closed-circuit television (CCTV) surveillance
: **Needs to be implemented; High**  
  Preventative/detective; can reduce risk of certain events; can be used after event for investigation

Locking cabinets (for network gear)
: **Needs to be implemented; High**  
  Preventative; increase integrity by preventing unauthorized personnel/individuals from physically accessing/modifying network infrastructure gear

Signage indicating alarm service provider
: **Needs to be implemented; Low**  
  Deterrent; makes the likelihood of a successful attack seem low

Locks
: **Needs to be implemented; Low**  
  Preventative; physical and digital assets are more secure

Fire detection and prevention (fire alarm, sprinkler system, etc.)
: **Needs to be implemented; High**  
  Detective/Preventative; detect fire in the toy store’s physical location to prevent damage to inventory, servers, etc.


