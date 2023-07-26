---
title: "Incident Handler's Journal"
date: 2023-07-26T09:48:13-07:00
categories: ["cybersecurity", "portfolio", "incident handler's journal"]
weight: 5
---
- [Back to index]({{< ref "cyber-security-portfolio-index" >}})
- [Entry 1]({{< ref "#entry-1" >}}): Analyzing ransomeware incident
- [Entry 2]({{< ref "#entry-2" >}}): Analyzing phishing email
- [Entry 3]({{< ref "#entry-3" >}}): Following up with playbook
- [Entry 4]({{< ref "#entry-4" >}}): Review on incident final report
- [Entry 5]({{< ref "#entry-5" >}}): Using Splunk to analyze malicious logins
- [Entry 6]({{< ref "#entry-6" >}}): Using Chronicle to analyze phishing attempts

## Entry 1
Date: 07/19/2023

### Description
Ransomeware damaged a clinic.

A small U.S. health care clinic experienced a security incident on Tuesday at
9:00 a.m. which severely disrupted their business operations.

The cause of the security incident was a phishing email that contained a
malicious attachment. Once it was downloaded, ransomware was deployed encrypting
the organization's computer files.

An organized group of unethical hackers left a ransom note stating that the
company's files were encrypted and demanded money in exchange for the decryption
key.

### Tool(s) used

Phishing email; Ransomware

### The 5 W's

Who
: An organized group of unethical hackers

What
: A phishing email compromised the system and deployed ransomware

When
: Tuesday 9:00 AM

Where
: A small US health care clinic

Why
: Clinic staff opened the phishing email attachment. The cybersecurity operator
  could not stop the ransomware’s encryption process.

### Additional notes
Were there any sign of heavy load in the system during the encryption phase?

---
## Entry 2
Date: 07/20/2023

### Description

In the SOC of a financial service company, We received an alert about a
suspicious file download to a staff’s computer. We also detected an intrusion by
our IDS.

After investigating the files’ SHA256 hash, we identified this is a malware
attack.

### Tool(s) used

Hashing and VirusTotal

### The 5 W's

Who
: An employee

What
: Phishing email compromise

When
: the email was received at 07/20/2023 1:11 pm, IDS alerts on 1:20

Where
: Endpoint (staff’s computer)

Why
: Our IDS did not block the email attachment because it was password protected

### Additional notes

How to protect downloading suspicious attachment which are password protected?

---
## Entry 3
Date: 07/20/2023

### Description
Ticket handling regarding [Entry 2]({{< ref "#entry-2" >}})

### Tool(s) used
Alert Ticket/Phishing incident response playbook

### The 5 W's

Who
: An employee

What
: Phishing email compromise

When
: the email was received at 07/20/2023 1:11 pm, IDS alerts on 1:20

Where
: Endpoint (staff’s computer)

Why
: Our IDS did not block the email attachment because it was password protected

### Additional notes
I updated the alert ticket and escalated it to the Level 2 SOC operator.

---
## Entry 4
Date: 07/20/2023

### Description
Review of a final report of an incident that was recognized on at 12/28/2022 7:20 pm PT.

### Tool(s) used
none

### The 5 W's

Who
: cybercriminal

What
: data theft

When
: recognized on at 12/28 and 12/31

Where
: a remote datacenter/web application

Why
: known vulnerability on web application was not patched

### Additional notes
Routine vulnerability scan and allowlist (access control) was recommended.

---
## Entry 5
Date: 07/20/2023

### Description

Using Splunk to identify a series of recorded events provided by a virtual
organization. Key findings are:
- There are 109,864 events in total
- There are 346 events of failed root ssh login attempts to the mail server
- There are 8,154 failed attempts using different system user names and port
  numbers with ssh access

### Tool(s) used
Splunk and Search Processing Language (SPL)

### The 5 W's
none

### Additional notes
Since all of attacks are targeting ssh protocol to the email server, I would
suggest blocking ssh access to email server from external of the network by
configuring the firewall.

---
## Entry 6
Date: 07/20/2023

### Description

Perform Chronicle SIEM analysis over specific events.
- A phishing attempt was detected, which contains the domain information
  `signin.office365x24.com`. According to VT information, this domain is a drop
  site of stolen credentials. We have to (1) identify all the phishing attempts
  to other employees, and (2) assess all access attempts of accessing the target
  domain, which indicates the successful manipulation by the attacker.
- On 1/88 security vendors reported the domain as malicious
- The log shows the device `ashton-davidson-pc` has both GET and POST access to
  the `/login.php` on `14:40:45 01/31/2023`
- `emil-palmer-pc` has POST access to the domain
- `warren-morris-pc` has POST access to a different domain which has the same IP
  address resolved.

### Tool(s) used
Chronicle and UDM search

### The 5 W's

Who
: A malicious attacker and owner of devices.

What
: 6 staff have accessed the target IP address, 3 of them had POST requests which
  indicate their credentials of them are compromised

When
: 01/31/2023 14:40:40 to 14:51:45

Where
: From internal network access to the malicious server which is likely to be
  located in Singapore.

Why
: Trapped by a phishing attempt simultaneously, which may indicate a lack of
  education in cybersecurity.

### Additional notes
- Need a further analysis of why `warren-morris-pc` has the same access but a
  different domain. We might have other phishing emails uncaught.
- A success rate of this phishing attempt is higher than normal. Need further
  security control.

