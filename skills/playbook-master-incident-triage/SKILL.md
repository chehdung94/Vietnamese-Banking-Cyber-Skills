---
name: playbook-master-incident-triage
description: Master playbook to classify and prioritize security incidents, determine severity, assign response teams, and initiate appropriate Tier 2 Incident Playbooks.
domain: cybersecurity
subdomain: incident-response
tags: [playbook, master, triage, severity-classification, routing, DFIR]
version: "1.1"
author: cybersecurity-skills-mode
license: Apache-2.0
---

# Playbook: Master Incident Triage

## Overview

Incident triaging is the critical first step in incident response. This skill provides a structured approach to classifying security incidents, determining severity, and initiating appropriate response procedures using standardized IR playbooks.

## When to Use

- New security alert requires initial assessment
- Determining if event constitutes a security incident
- Deciding escalation path and response team
- Prioritizing multiple concurrent incidents
- Initial documentation for incident response

## Incident Classification Matrix

| Category | Examples | Severity | Response Time |
|----------|----------|----------|---------------|
| **Critical** | Ransomware, Active breach, Data exfiltration | P1 | Immediate |
| **High** | Malware outbreak, Compromised admin account | P2 | < 1 hour |
| **Medium** | Phishing success, Unauthorized access | P3 | < 4 hours |
| **Low** | Failed login attempts, Policy violation | P4 | < 24 hours |

## Severity Criteria

### P1 - Critical
- [ ] Active ransomware encryption in progress
- [ ] Confirmed data breach or exfiltration
- [ ] Business-critical system compromised
- [ ] Multiple systems with active attacker presence
- [ ] C2 communication confirmed

### P2 - High
- [ ] Malware detected on multiple endpoints
- [ ] Privileged account compromised
- [ ] Lateral movement detected
- [ ] Attacker's initial foothold confirmed
- [ ] Business-sensitive data at risk

### P3 - Medium
- [ ] Phishing with successful user engagement
- [ ] Single endpoint with suspicious activity
- [ ] Unauthorized access attempt (blocked)
- [ ] Policy violation with potential impact
- [ ] Social engineering attempt detected

### P4 - Low
- [ ] Failed brute force attempts
- [ ] Single failed login anomaly
- [ ] Minor policy violation (no data impact)
- [ ] Suspicious but unconfirmed activity
- [ ] Equipment loss/theft (no data)

## Triage & Routing Steps

### Step 1: Initial Identification

**Questions to Answer:**
- What systems are affected?
- What is the timeline of events?
- Who reported or detected the incident?
- What data/systems are at risk?

**Documentation Required:**
```json
{
  "incident_id": "INC-YYYYMMDD-XXX",
  "detection_source": "EDR/SIEM/User Report",
  "timestamp_detected": "YYYY-MM-DD HH:MM:SS",
  "systems_affected": [],
  "initial_severity": "P1/P2/P3/P4"
}
```

### Step 2: Initial Assessment

**Check for:**
- [ ] Is the attack still ongoing?
- [ ] Is there evidence of lateral movement?
- [ ] Has sensitive data been accessed?
- [ ] Are backup systems available?
- [ ] Has the attacker been contained?

### Step 3: Escalation & Routing Decision (Call Tier 2 Playbooks)

Based on the initial assessment, route the incident to the appropriate Tier 2 Playbook:

| Incident Type | Condition | Action (Route To) |
|---------------|-----------|-------------------|
| **Phishing** | Malicious email, credential harvesting, malware attachment | `playbook-phishing-response` |
| **Malware/Ransomware** | File encryption, unknown processes, C2 traffic | `playbook-malware-response` |
| **Data Leak** | Unauthorized access, large data transfer | `playbook-data-leak-response` |
| **Insider Threat** | Abnormal behavior by authenticated user | `playbook-insider-threat-response` |
| **General/Unknown** | P1/P2 Active attack but type unknown | Escalate to CIRT, Isolate systems |

### Step 4: Initiate Tier 2 Playbook

**Action for Agent:** Once the incident type is determined, stop this playbook and immediately load the `SKILL.md` of the corresponding Tier 2 playbook to present the specific evidence checklist to the user.

## Vietnamese Banking Context

**Critical Assets in Banking:**
| Asset Category | Priority | Examples |
|----------------|----------|----------|
| Core Banking | P1 | T24, Flexcube, BaNKS |
| Customer Data | P1 | PII, Account data |
| Payment Systems | P1 | SWIFT, Napas, Internet Banking |
| Internal Network | P2 | AD, DNS, Domain Controllers |
| Office Systems | P3 | Email, SharePoint, Office 365 |

**Escalation Contacts:**
- CISO: Immediate for P1
- IT Security Team: All incidents
- Legal/Compliance: Data breach
- NHNN: Regulatory reporting (if required)

## Output Format

```json
{
  "incident_id": "INC-YYYYMMDD-XXX",
  "classification": "Category/Subcategory",
  "severity": "P1/P2/P3/P4",
  "response_team": [],
  "playbook": "IR-PLAYBOOK-NAME",
  "immediate_actions": [],
  "affected_assets": [],
  "initial_indicators": [],
  "escalation_status": "Yes/No",
  "documentation": "Link to incident record"
}
```

## Validation Checklist

- [ ] All required fields populated
- [ ] Severity assigned based on criteria
- [ ] Appropriate response team notified
- [ ] Initial containment actions taken (if needed)
- [ ] Incident documented in ticketing system
- [ ] Escalation completed if required

## References

- NIST SP 800-61 (Incident Handling Guide)
- SANS IR Playbooks
- IBM IRP Methodology
- ISO 27035 (Information Security Incident Management)
