# Privilege Escalation Chain Documentation (Lab)
### Scenario: Family-Owned Healthcare Clinic (Simulated) — Week 3, SDC Internship

**Author:** Sawera — Member 4, Individual Contributor
**Scope:** Authorized lab target only (TryHackMe — Linux PrivEsc Arena). No real or production systems were tested.

---

## 1. Scope Statement
This engagement simulates an internal Linux web server belonging to a Family-Owned Healthcare Clinic. Testing was performed exclusively against an authorized, intentionally vulnerable lab machine (TryHackMe's Linux PrivEsc Arena) to demonstrate a full privilege escalation chain from a low-privileged foothold to root. No real client, patient, or production data or systems were involved at any stage.

## 2. Tools Used
- **Metasploit / Kali toolset** (via TryHackMe AttackBox)
- SSH (initial access)
- `find`, `strings` (enumeration and binary analysis)
- Shell scripting (exploit delivery)

## 3. Attack Chain — What Was Done and Found

| Stage | Action | Result |
|---|---|---|
| Initial Access | SSH login with provided low-privilege credentials (`TCM`) | Shell access as standard user |
| Enumeration | `find / -perm -u=s -type f 2>/dev/null` | Identified SUID binaries, including a non-standard one: `/usr/local/bin/suid-env` |
| Analysis | `strings /usr/local/bin/suid-env` | Binary is SUID-root and calls `service apache2 start` using a relative (non-absolute) path — a PATH-based privilege escalation vulnerability |
| Exploitation | Created a malicious `service` script in `/tmp` containing `/bin/bash -p`, made it executable, and prepended `/tmp` to `$PATH` before running the SUID binary | The SUID binary executed the attacker-controlled `service` script with root privileges |
| Verification | `id` | `uid=0(root) gid=0(root)` — confirmed full root access |

**Root cause:** The SUID binary trusted the `$PATH` environment variable and called an external command (`service`) without specifying its absolute path (`/usr/sbin/service`), allowing any user to override which binary gets executed with root privileges.

## 4. Severity
**High** — Any authenticated local user can escalate to full root access with a two-command exploit, no advanced tooling required.

## 5. Remediation Recommendation
- Modify the SUID binary to call external commands using their **full absolute path** (e.g. `/usr/sbin/service`), never relying on `$PATH`.
- Where possible, avoid SUID binaries invoking shell commands or external programs entirely; use direct system calls instead.
- Regularly audit SUID/SGID binaries on production systems with `find / -perm -u=s -type f` and review any non-standard entries.

## 6. What I Would Improve With More Time
Given more time, I would build a small automated script to scan a system for SUID binaries that call external commands via relative paths (using `strings` + pattern matching), rather than manually inspecting each binary — turning this manual check into a repeatable detection tool for future engagements.

---
*All testing was conducted in an isolated, authorized lab environment (TryHackMe) in accordance with the internship's authorized-testing-only policy.*
