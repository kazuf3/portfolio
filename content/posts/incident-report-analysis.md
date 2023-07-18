---
title: "Incident Report Analysis"
date: 2023-07-18T10:55:20-07:00
categories: ["portfolio", "cybersecurity", "incident report"]
---
[Back to incident report]({{< ref "incident-report" >}})

# Incident report analysis

## Instructions

As you continue through this course, you may use this template to record your findings after completing an activity or to take notes on what you've learned about a specific tool or concept. You can also use this chart as a way to practice applying the NIST framework to different situations you encounter.

## Summary

The organization recently experienced a DDoS attack, which compromised the
internal network for two hours until it was resolved.

During the attack, the organization’s network services suddenly stopped
responding due to an incoming flood of ICMP packets. Normal internal network
traffic could not access any network resources. The incident management team
responded by blocking incoming ICMP packets, stopping all non-critical network
services offline, and restoring critical network services.

Identify
: The attacker targeted the company with ICMP flood attack. The entire network was affected.

Protect
: The cybersecurity team implemented:
  - a new firewall rule to regulate incoming ICMP packet rate
  - IDS/IPS system to filter out some ICMP traffic based on suspicious characteristics

Detect
: The cybersecurity team implemented:
  - A new firewall rule to verify incoming packets with a spoofed internal IP address
  - Network monitoring software to detect abnormal traffic patterns

Respond
: For future security events, the cybersecurity team will isolate affected systems to prevent further disruption to the network. As a preventative measure, the cybersecurity team will also implement network segmentation in order to minimize the disruption.

Recover
: To recover from a DDoS attack, access to the network needs to be restricted. In the future, incoming ICMP packets are regulated by the firewall, so that ICMP flood attacks will not succeed.  
  Once the cybersecurity team has implemented network segmentation, the future recovery process of critical services will be faster because it is identified in a segment of the network.  
  For non-critical segments, once ICMP flood packets are blocked at the
  firewall, they will automatically be recovered.

