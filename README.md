# Privilege Escalation Chain Documentation (Lab)
### Family-Owned Healthcare Clinic (Simulated) — Week 3, SDC Internship

**Author:** Sawera — Member 4, Individual Contributor
**Scope:** Authorized lab target only (TryHackMe — Linux PrivEsc Arena). No real or production systems were tested.

## Summary
This project documents a full privilege escalation chain from a low-privileged user to root on an authorized lab machine, simulating an internal server for a healthcare clinic. A SUID binary calling an external command without an absolute path was exploited via PATH hijacking to gain root access.

## Contents
- `Privilege_Escalation_Report.pdf` — full report with scope, attack chain, severity, remediation, and evidence
- Screenshots — terminal evidence for each stage (initial access, enumeration, analysis, exploitation, verification)

## Tools Used
Metasploit/Kali toolset (TryHackMe AttackBox), SSH, `find`, `strings`

## Authorization
All testing was conducted in an isolated, authorized lab environment (TryHackMe) in accordance with the internship's authorized-testing-only policy.
