# SOC Automation Demo

**A fully operational security automation pipeline built from scratch attack simulation, detection, enrichment, and forensic investigation, end to end.**

This isn't a tutorial follow-along. It's a working system I designed, built, broke, and fixed until it did what a real SOC automation stack does: take a raw alert and turn it into an enriched, triaged, documented incident, automatically.

---

## What This Does

An attacker runs a brute force attack against a system.

Wazuh detects it. N8N picks up the alert via webhook, routes it by severity, enriches the IOC against VirusTotal, evaluates whether it's malicious, and triggers a Velociraptor forensic investigation on the affected endpoint, automatically.

Zero manual steps between "attack happened" and "endpoint forensics running."

---

## The Stack

| Tool | Role |
|------|------|
| **Wazuh** | SIEM — detects and generates alerts from endpoint events |
| **N8N** | Automation engine — webhook receiver, alert router, orchestrator |
| **VirusTotal API** | Threat enrichment — IOC reputation lookup integrated into the pipeline |
| **Velociraptor** | Endpoint forensics — live investigation and log analysis on affected hosts |
| **Caldera** | Adversary simulation — used to generate real attack traffic to test the pipeline |

---

## Architecture

```
[Attack Simulation]          [Detection]           [Automation]           [Forensics]
  Caldera Agent      →→→     Wazuh SIEM    →→→    N8N Workflow    →→→   Velociraptor
(SSH brute force,            (1,500+ alerts          ├── Switch            (live endpoint
 credential access,           captured,               │   (by alert type)   investigation,
 SUDO abuse)                 rule-matched)            ├── VirusTotal API    SSH log review,
                                                      ├── Malicious IF/ELSE notebook docs)
                                                      └── Velociraptor
```

---

## Demo Video

> **Full walkthrough — tools overview + live pipeline demo: 8 minutes**

[![SOC Automation Lab Demo](https://img.shields.io/badge/▶_Watch_Demo-SOC_Automation_Pipeline-brightgreen?style=for-the-badge)](https://youtu.be/FNTELJc-1KU)

**What the video covers:**

| Timestamp | What You'll See |
|-----------|----------------|
| `0:00` | Wazuh live dashboard — real alert data, severity tiers |
| `0:30` | N8N workflow canvas — full pipeline architecture |
| `1:30` | Alert routing logic — switch node by alert type |
| `2:00` | VirusTotal API integration — live IOC enrichment node |
| `2:30` | Malicious/benign decision logic — IF condition config |
| `3:00` | Caldera — 1,837 ATT&CK abilities, adversary selection |
| `3:30` | Starting a real attack operation in Caldera |
| `4:00` | Wazuh firing in real time — 1,525+ alerts during attack |
| `4:30` | Caldera executing credential-access tactic (SUDO Brute Force) |
| `5:00` | Wazuh ossec.conf — N8N webhook configured at SIEM level |
| `5:30` | Alert table post-attack — PAM failures, SSH events captured |
| `6:00` | Terminal — SSH brute force loop executing live |
| `6:30` | N8N execution history — multiple successful pipeline runs |
| `7:00` | Velociraptor — forensic investigation, investigation notebooks |
| `7:30` | Velociraptor case with SSH brute force logs — full chain complete |

---

## How the Pipeline Works

### 1. Attack Simulation (Caldera)
I use MITRE's Caldera framework to run structured adversary simulations not synthetic log injection, but actual attack execution against a live agent. The demo uses a credential-access operation (SSH brute force / SUDO abuse) that generates real authentication failure events.

### 2. Detection (Wazuh)
Wazuh monitors the agent and fires alerts against its ruleset. During the Caldera operation, it captures 1,500+ events including PAM authentication failures, login session events, and SUDO execution attempts. The ossec.conf is configured to forward alerts above a set severity threshold to N8N via webhook.

### 3. Automation (N8N)
The N8N workflow receives the Wazuh webhook, then:
- **Switch node** routes the alert by `rule.id` or alert type
- **VirusTotal node** queries the IOC (IP, hash, or domain) against the VirusTotal API
- **IF/Malicious node** evaluates the VirusTotal response against a malicious score threshold
- **Merge node** consolidates enriched data
- **Velociraptor integration** — pipeline triggers forensic data collection on the affected endpoint

### 4. Endpoint Forensics (Velociraptor)
Velociraptor handles the investigation layer. It collects live endpoint data — SSH authentication logs, running processes, active connections — and documents findings in investigation notebooks. The demo shows two notebooks: "Detecting fake ssh" and "Detecting caldera," with raw SSH brute force log entries captured directly from the endpoint during the attack. This is active forensic investigation, not just alert logging.

---

## What I Learned Building This

**The automation logic is the easy part.** The hard part is deciding what goes into the pipeline and what stays with a human analyst. Automating too aggressively creates cases that overwhelm analysts. Automating too conservatively defeats the purpose.

**VirusTotal enrichment has significant false negative risk for internal/private IPs.** The pipeline needs explicit handling for RFC1918 space — otherwise you're sending private IP ranges to a public reputation API and getting nothing useful back.

**Wazuh's webhook integration requires careful ossec.conf scoping.** Forwarding every alert creates noise that breaks downstream automation. Threshold tuning at the Wazuh level (not the N8N level) is the right approach.

**Caldera is significantly more useful for detection validation than static log replay.** Running an actual attack operation produces realistic event sequences, realistic timing, and realistic log volumes — which exposes gaps in detection coverage that sanitized datasets don't.

---


## Related Work

- **[Attack Technique Analysis](https://github.com/ManishRawat21/attack-technique-analysis)** — Sigma detection rules for DLL Sideloading, and PowerShell obfuscation, validated against this lab's attack simulation environment

---

*Built by Manish Rawat · [LinkedIn](https://linkedin.com/in/rawat-manish-mr2000) · [Email](mailto:Rawatmanish21@outlook.com)*
