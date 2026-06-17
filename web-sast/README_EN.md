# Web SAST — Web Front-End Static Security Scanner

<p align="center">
  <strong>🛡️ One URL, four-phase automated scan, HTML security audit report in under 2 minutes</strong>
</p>

<p align="center">
  <img src="https://img.shields.io/badge/version-2.0.0-blue" alt="version">
  <img src="https://img.shields.io/badge/license-MIT-green" alt="license">
  <img src="https://img.shields.io/badge/standard-agentSkills.io-orange" alt="standard">
</p>

---

## Overview

**Web SAST** is an AI Agent Skill for static application security testing (SAST) of web front-ends. It performs a four-phase automated scan on any given URL and produces a **structured HTML security audit report** with severity ratings, remediation code examples, and compliance impact analysis (printable to PDF via browser).

### Capabilities

| Capability | Description |
|------------|-------------|
| 🔍 **HTTP Security Headers** | Detects CSP, HSTS, X-Frame-Options, and 4 other security headers |
| 🔐 **SSL/TLS Certificate** | Checks expiration, issuer, and SAN coverage |
| 📦 **Source Extraction** | Auto-downloads HTML + external JS/CSS resources |
| 🧪 **15 Vulnerability Rules** | Covers hardcoded secrets, XSS, CSRF, PII leaks, and more |
| 📊 **Graded Audit Report** | 9-section HTML report with fix code examples |

### Limitations

- ❌ No backend penetration testing (SQL injection, command injection require interactive probing)
- ❌ No DDoS / network-layer testing
- ❌ No scanning of login-gated pages (unless auth credentials are provided)
- ❌ Not a replacement for OWASP ZAP / Burp Suite
- ❌ Never launches attacks — read-only analysis, zero side effects

---

## Quick Start

### Use with AI Tools

Install the skill file (`SKILL.md`) into a supported AI tool:

**WorkBuddy / Claude Code / Cursor**
```bash
# Copy the entire directory into the skills folder
cp -r web-sast/ ~/.workbuddy/skills/
# For Cursor: cp -r web-sast/ ~/.cursor/skills/
```

**Other agentSkills.io-compatible tools**
```bash
git clone https://github.com/chen-mingsong/web-sast.git
# Place web-sast/ into the tool's skills directory
```

### CLI Usage

Run the automated scan script directly (depends only on system tools: `curl`, `openssl`, `grep`):

```bash
chmod +x references/audit.sh
./references/audit.sh https://example.com/login
```

### In Conversation

Once installed, simply say in an AI conversation:

> "Scan https://example.com for vulnerabilities"
>
> "Check this website for XSS and CSRF"
>
> "Run a security audit on this page"

The AI will automatically load the skill and run the four-phase scan.

---

## Scan Flow

```
URL → Phase 1: Surface Scan → Phase 2: Source Extraction → Phase 3: Deep Scan → Phase 4: Report Generation
```

| Phase | Description | Duration |
|-------|-------------|----------|
| **Phase 1** | HTTP headers + SSL certificate check | ~10s |
| **Phase 2** | HTML download + JS/CSS extraction | ~15s |
| **Phase 3** | Pattern matching with 15 rules | ~30s |
| **Phase 4** | HTML report (browser print to PDF) | ~5s |

---

## Detection Rules

### Critical (5 rules)
- `CRIT-01` Hardcoded secrets / backdoor passwords
- `CRIT-02` Math.random() for security purposes
- `CRIT-03` Client-side CAPTCHA generation
- `CRIT-04` PII in URL query parameters
- `CRIT-05` All security headers missing

### High (6 rules)
- `HIGH-01` eval() / new Function() with dynamic input
- `HIGH-02` innerHTML assignment (XSS sink)
- `HIGH-03` Missing CSRF token in forms
- `HIGH-04` console.log with sensitive data
- `HIGH-05` Server version exposed in headers
- `HIGH-06` Third-party trackers on PII pages

### Medium (4 rules) + Low (2 rules)
Full rule set: `references/rules.md`

---

## Report Output

HTML security audit report with **9 sections**:

1. **Executive Summary** — four-color stat cards + overall rating
2. **Target Info** — URL, scan time, tech stack
3. **Scan Flow Review** — phase-by-phase status
4. **HTTP Headers & SSL** — complete status table
5. **Vulnerability Details** — grouped by severity, with code snippets + fixes
6. **Remediation Priority** — P0/P1/P2 sorting + effort estimates
7. **Tool Comparison** — vs OWASP ZAP / Burp Suite
8. **Recommendations** — numbered action items
9. **Audit Limitations** — methodology, scope, false positive notes

> Print-friendly CSS included — `Cmd+P` → "Save as PDF" for one-click export.

---

## File Structure

```
web-sast/
├── README.md                          # Bilingual documentation (Chinese + English)
├── README_EN.md                       # English-only documentation
├── SKILL.md                           # Core skill file (agentSkills.io compliant)
└── references/
    ├── rules.md                       # 15 detection rules with grep patterns
    ├── international-standards.md     # agentSkills.io specification guide
    └── audit.sh                       # One-click automated scan script
```

---

## Compatibility

| AI Tool / Platform | Support |
|--------------------|---------|
| WorkBuddy | ✅ Native |
| Claude Code | ✅ Supported (agentSkills.io) |
| Cursor | ✅ Supported (skills directory) |
| Other agentSkills.io tools | ✅ Universal format |
| CLI (bash) | ✅ `references/audit.sh` |

### System Dependencies

- `curl` — built-in (macOS / Linux)
- `openssl` — built-in
- `grep` — built-in
- `bash` — built-in

---

## Comparison with Professional Tools

| Dimension | Web SAST | OWASP ZAP | Burp Suite Pro |
|-----------|----------|-----------|----------------|
| **Type** | AI-driven static analysis | Automated DAST + proxy | Interactive DAST + proxy |
| **Speed** | 30s ~ 2min | 5 ~ 30min | Unlimited |
| **Setup** | Zero-install (curl + grep) | Java required | Java + paid license |
| **Depth** | Source pattern matching | Active probing + passive crawl | Full pentest suite |
| **Best for** | Quick triage, self-audit | CI/CD security gate | Compliance, red team |

---

## Related: Mini Program SAST

| | Web SAST | Mini Program SAST |
|------|----------|-------------------|
| **Target** | Web pages (H5 / SPA) | WeChat Mini Program .wxapkg |
| **Input** | URL | Mini Program AppID |
| **Method** | HTTP requests + source download | Local cache strings extraction |
| **Rules** | 15 web front-end rules | 17 mini program rules |

---

## Disclaimer

This tool is for authorized security auditing and self-assessment only. Users must ensure:
- Legal authorization to scan the target
- No exploitation of discovered vulnerabilities against third-party services
- Compliance with local laws and regulations

The author assumes no liability for misuse.

---

## License

MIT License — see [LICENSE](LICENSE) file.

---

## FAQ

**Q: How accurate are the scan results? Are there false positives?**
A: Pattern-based matching has a non-zero false positive rate. Each finding includes context code snippets — you'll need to evaluate whether it constitutes a real risk in your business logic.

**Q: Does scanning affect the target website?**
A: No. Only `curl` GET requests for publicly accessible pages — zero side effects.

**Q: Can it scan login-gated pages?**
A: Not by default. With Cookie or Token provided, you can modify the curl commands in audit.sh to include auth headers.

**Q: How do I fix discovered vulnerabilities?**
A: Every finding includes specific front-end and back-end remediation code examples.
