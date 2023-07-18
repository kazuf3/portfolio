---
title: "Stakeholder Memorandum"
date: 2023-07-17T21:04:23-07:00
categories: ["cybersecurity", "portfolio", "security-audit"]
---
[Back to Security Audit]({{< ref "security-audit" >}})

# Stakeholder memorandum


TO: IT Manager, Stakeholders  
FROM: Kazuhiro Funakoshi  
DATE: 07/13/2023  
SUBJECT: Internal IT Audit Findings and Recommendations  

Dear Colleagues,

Please review the following information regarding the Botium Toys internal audit scope, goals, critical findings, summary and recommendations.

## Scope

- The following systems are in scope: accounting, endpoint detection, firewalls,
intrusion detection system, security information and event management (SIEM)
tool.

- Ensure current user permissions, controls, procedures, and protocols in
place align with necessary compliance requirements.

## Goals
- To adhere to the National Institute of Standards and Technology Cybersecurity
  Framework (NIST CSF)
- Establish a better process for their systems to ensure they are compliant
- Fortify system controls
- Implement the concept of least permissions when it comes to user credential
  management
- Establish their policies and procedures, which include their playbooks
- Ensure they are meeting compliance requirements

## Critical findings (must be addressed immediately)

- Multiple controls need to be implemented:
  - Least privilege
  - Access control policies
  - Account management policies
  - Antivirus (AV) software
  - CCTV surveillance
  - Locking cabinets (for network equipment)
  - Fire detection
- Need to implement policies to meet GDPR and PCI DSS requirements
- Need to enforce policies to meet SOC type 1 and SOC type 2 guidance for:
  - User access control
  - Overall data safety

## Findings (should be addressed, but no immediate need)

- Medium priority:
  - Disaster recovery plans
  - Password policies
  - Separation of duties
  - Encryption
  - Backups
  - Password management system
  - Manual monitoring, maintenance, and intervention
- Low priority:
  - Time-controlled safe
  - Adequate lighting
  - Signage indicating the alarm service provider
  - Locks

## Summary/Recommendations

It is recommended that critical findings be resolved. Meanwhile, some other
findings are lower prioritized based on the existence of a firewall. However,
the firewall policies need to be examined regularly in detail. For instance,
both password management and access control are crucial if a threat actor once
obtains access to the internal network. Based on the concept of protection in
deep, these findings are also recommended to be resolved.


