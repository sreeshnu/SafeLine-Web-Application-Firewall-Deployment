# SafeLine Web Application Firewall Deployment

Deployment of **SafeLine WAF** in front of an Apache-hosted web application, with a live SQL Injection attack used to verify the WAF actually detects and blocks malicious requests — not just that it's installed.

---

## 🎯 Objective

Go beyond "installing a WAF" and prove it works, by attacking the protected application directly and confirming the malicious request gets blocked and logged.

## 🧰 Tools & Stack

| Category | Tools |
|---|---|
| Web Server | Apache (Ubuntu, listening on port 81) |
| WAF | SafeLine |
| Attack Simulation | Manual SQL Injection via URL parameter |

## 🏗️ Setup Overview

```
┌────────────┐   requests   ┌───────────────┐   filtered traffic   ┌───────────────┐
│  Attacker   │ ────────────▶│ SafeLine WAF   │ ─────────────────────▶│ Apache Web App │
│ (SQLi test) │               │ (reverse proxy)│                        │  (port 81)     │
└────────────┘               └────────┬──────┘                        └───────────────┘
                                       │
                                       ▼
                               Blocked request logs
```

## 🔍 What Was Done

1. **Installed SafeLine** on Ubuntu using its official install script, run via `curl` piped to `bash` with the `--en` flag for English setup.
2. **Logged into the SafeLine admin panel** (served on port `9443`) using the auto-generated admin credentials shown at the end of installation.
3. **Registered the target application** under *Applications → Add Application*:
   - Domain: `secure-lab` (mapped to `192.168.1.99` via `/etc/hosts` for local testing)
   - Listening port: `80`
   - Mode: Reverse Proxy
   - Upstream: `http://192.168.1.99:81` (the real Apache app)
4. **Sent a live SQL Injection payload** at the protected app through the browser:
   ```
   http://secure-lab/index.php?id=1' OR '1'='1
   ```
5. **Observed the block** — the browser returned an **"Access Forbidden"** page instead of reaching the application, confirming SafeLine intercepted the payload before it hit Apache.
6. **Reviewed the attack log** under *Attacks → Log* in the SafeLine dashboard, which recorded the blocked request with its attack type (`SQL Inj`), source IP, and timestamp.

## 📊 Key Findings

- The classic auth-bypass payload `' OR '1'='1` was blocked immediately by SafeLine's default rule set — no custom rule tuning was needed for this attack class.
- The Attacks log distinguished attack types automatically (e.g. `SQL Inj` vs `XSS`), which is useful for triaging real incidents rather than treating every block the same way.
- Because SafeLine sits as a reverse proxy in front of the real app, the application itself needed zero code changes to gain this protection.

## 🛡️ Skills Demonstrated

`Web Application Security` · `WAF Deployment & Configuration` · `Reverse Proxy Setup` · `SQL Injection Testing` · `Security Log Analysis`

---

*Part of my cybersecurity portfolio — see more at [github.com/yourusername](https://github.com/yourusername)*
