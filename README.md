# SOC Automation Lab

This repository documents the design, detection logic, automation workflows, and lessons learned from building an automated SOC demo using open-source tools.

The project focuses on **how alerts move from detection to investigation and notification**, and what breaks along the way.

This is not a production-ready deployment. It is a **learning and reference lab**.

---

## Architecture Overview

**Flow:**

1. MITRE Caldera simulates attacker activity
2. Wazuh detects the behavior on a Linux endpoint
3. N8N enriches alerts and automates notification and ticketing
4. Velociraptor validates the activity for forensic confidence

Detection → Enrichment → Notification → Validation

---

## Tools Used

- Wazuh (SIEM / detection)
- N8N (automation / SOAR-style workflows)
- MITRE Caldera (attack simulation)
- Velociraptor (forensic validation)

---

## What This Repository Contains

- Detection logic and thresholds
- Automation decision flow
- Attack simulation context
- Forensic validation queries
- Operational lessons and pitfalls
- False positives and noise considerations

---

## Medium Articles

This repository accompanies the following write-ups:

- *A Senior Challenged Me to Build an Automated SOC Demo. Here’s What I Built.*
- *Wazuh + N8N Integration Almost Beat Me — But Giving Up Wasn’t an Option*

(Links will be added)

---

## Disclaimer

All configurations are simplified and sanitized.
No secrets, API keys, or production-ready files are included.
