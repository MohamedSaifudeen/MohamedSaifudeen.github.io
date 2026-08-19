---
title: "Enterprise 101 — Home SOC Lab"
status: "In Progress"
stack: [Active Directory, DNS, DHCP, Wazuh, SIEM]
github_url: ""
writeup_url: ""
summary: "A 7-VM lab modeling a small enterprise network — AD, DNS, DHCP, and a SIEM (Wazuh) — built to generate and investigate real telemetry."
---

## Why I built this

Guided rooms are great for learning a specific skill, but they hand you the alert. I wanted
a network I built myself — with real Active Directory, DNS, and DHCP behavior — so I could
generate my own telemetry, break things on purpose, and practice detection the way an actual
SOC analyst would: starting from a SIEM alert with no answer key.

## Architecture

The lab currently runs **5 virtual machines**:

<!-- TODO: fill in the exact VM roles/OS versions, e.g.
- Domain Controller (Windows Server 2022) — AD DS, DNS, DHCP
- SIEM (Ubuntu) — Wazuh manager + indexer
- 2x Windows 10/11 endpoints (domain-joined, Wazuh agent installed)
- Attacker box (Kali) for generating traffic to detect
- pfSense/router VM for network segmentation
-->

| Role | Status |
|---|---|
| Domain Controller | Windows Server — Active Directory, DNS, DHCP configured, Wazuh agent connected |
| Workstation | Windows 11 — Wazuh agent connected |
| Workstation | Ubuntu Desktop — Wazuh agent connected |
| Corporate server | Baseline snapshot taken |
| Security server | Running Wazuh Indexer, Server, and Dashboard |
| Attacker machine | Kali Purple — may add a standard Kali box later for a fuller offensive toolset |
| Security Onion | Provisioned, not currently in active use |

All endpoints forward logs to Wazuh, which is where detection and investigation work
happens — the same workflow as a real SOC console rather than a pre-filtered CTF room.

## What I'm using it for

- Standing up AD/DNS/DHCP correctly enough that it behaves like a real small-business network
- Wazuh is ingesting and alerting on endpoint + Windows Event Log telemetry — next is writing custom detection rules instead of relying on    defaults
- Practicing detection engineering: writing and tuning rules, not just consuming pre-built ones
- Simulating common attack techniques against my own environment and investigating the
  resulting alerts as if they came in cold

## Status

This is an active, ongoing build rather than a finished project — the write-up here will
grow as I add more of the network (segmentation, additional log sources, and specific
detections I've written and tested).

<p class="todo-note"><strong>TODO —</strong> once you have screenshots (network diagram,
Wazuh dashboard, a specific alert you investigated), add them here and link the GitHub repo
/ detailed writeup in this file's front matter (<code>github_url</code>, <code>writeup_url</code>).</p>
