# Mini Program SAST — WeChat Mini Program Static Security Scanner

<p align="center">
  <strong>📱 Provide a Mini Program AppID, auto-locate cached .wxapkg packages, extract code, and produce a graded security audit report</strong>
</p>

<p align="center">
  <img src="https://img.shields.io/badge/version-1.0.0-blue" alt="version">
  <img src="https://img.shields.io/badge/license-MIT-green" alt="license">
  <img src="https://img.shields.io/badge/standard-agentSkills.io-orange" alt="standard">
</p>

---

## Overview

**Mini Program SAST** is an AI Agent Skill for static security scanning of WeChat Mini Programs. Given a Mini Program AppID, it automatically locates cached `.wxapkg` package files in the local WeChat client, extracts readable strings, and performs pattern-matching vulnerability scanning. The result is a **structured HTML security audit report** with severity ratings and remediation examples (printable to PDF via browser).

### Capabilities

| Capability | Description |
|------------|-------------|
| 📦 **Auto Cache Location** | Supports macOS direct/AppStore/Windows WeChat cache paths |
| 🔓 **Code Extraction** | Extracts readable strings from binary .wxapkg packages |
| 🧪 **17 Specialized Rules** | Covers setStorageSync plaintext storage, login code persistence, AppSecret hardcoding, etc. |
| 🔗 **API Endpoint Analysis** | Auto-extracts and lists all API endpoint URLs |
| 📊 **Graded Audit Report** | 10-section HTML report with fix code examples |

### Limitations

- ❌ Cannot scan Mini Programs never opened locally (no cached .wxapkg)
- ❌ No backend penetration testing for Mini Program services
- ❌ Cannot bypass WeChat's encrypted transport layer
- ❌ Cannot decrypt custom-encrypted .wxapkg packages
- ❌ Not a replacement for WeChat's official security scanner

---

## Prerequisites

| Condition | Description | How to Verify |
|-----------|-------------|---------------|
| **① Mini Program opened locally** | Must have opened it at least once in the desktop WeChat client | Open WeChat → search mini program → enter any page |
| **② Cache not cleared** | .wxapkg files exist in WeChat cache directory | Skill auto-detects |
| **③ Correct AppID** | Unique identifier, typically a 16-character string starting with `wx` | See below |
| **④ Mac / Windows computer** | Requires local filesystem access | — |

### AppID Retrieval

- **Method 1 (admin panel)**: Log into WeChat Official Accounts Platform → Dev Management → Dev Settings → AppID
- **Method 2 (in WeChat)**: Tap mini program → top-right `···` → "Mini Program Homepage" → AppID visible

### Cache File Locations

| OS | Cache Path |
|----|------------|
| **macOS (direct download)** | `~/Library/Containers/com.tencent.xinWeChat/Data/.wxapplet/packages/{AppID}/` |
| **macOS (App Store)** | `~/Library/Containers/com.tencent.xinWeChat/Data/Documents/app_data/radium/users/*/applet/packages/{AppID}/` |
| **Windows** | `%USERPROFILE%\Documents\WeChat Files\Applet\{AppID}\` |

---

## Quick Start

### Use with AI Tools

**WorkBuddy / Claude Code / Cursor**
```bash
cp -r mini-program-sast/ ~/.workbuddy/skills/
# For Cursor: cp -r mini-program-sast/ ~/.cursor/skills/
```

**Other agentSkills.io-compatible tools**
```bash
git clone https://github.com/chen-mingsong/mini-program-sast.git
# Place mini-program-sast/ into the tool's skills directory
```

### CLI Usage

```bash
chmod +x references/audit.sh
./references/audit.sh wx1234567890abcdef
```

### In Conversation

Once installed, say in an AI conversation:

> "Scan mini program wx1234567890abcdef"
>
> "Check for key leaks in this mini program"
>
> "Audit this mini program's security"

---

## Scan Flow

```
AppID → Phase 0: Pre-check → Phase 1: Locate & Extract → Phase 2: Deep Scan → Phase 3: Report
```

| Phase | Description | Duration |
|-------|-------------|----------|
| **Phase 0** | Prerequisites check (cache directory location) | ~5s |
| **Phase 1** | Locate wxapkg + strings extraction | ~10s |
| **Phase 2** | Pattern matching with 17 rules | ~30s |
| **Phase 3** | HTML report (browser print to PDF) | ~5s |

---

## Detection Rules

### Critical (5 rules)
- `CRIT-01` Token/credentials in plaintext via wx.setStorageSync
- `CRIT-02` wx.login code persisted to storage
- `CRIT-03` openId/unionId passed via URL query parameters
- `CRIT-04` AppSecret / payment key hardcoded in frontend
- `CRIT-05` Mock mode enabled in production build

### High (6 rules)
- `HIGH-01` console.log with sensitive data
- `HIGH-02` API request params containing plaintext PII
- `HIGH-03` Payment signing with MD5
- `HIGH-04` Non-HTTPS API endpoints
- `HIGH-05` Unsafe navigation with user-controlled URLs
- `HIGH-06` Hardcoded 3rd-party appId / corpId

### Medium (4 rules) + Low (4 rules)
Full rule set: `references/rules.md`

---

## Report Output

HTML security audit report with **10 sections**:

1. **Executive Summary** — colored stat cards + encryption/limitation notes
2. **Target Info** — AppID, scan time, cache path, package details
3. **Scan Flow Review** — Phase 0→3 status
4. **Package Analysis** — file name, size, magic number, version, entropy
5. **Coverage Matrix** — rule × package hit matrix
6. **Findings & Assessment** — per-finding with tags
7. **Risk Rating** — overall rating + encryption limitation statement
8. **Comparison with Other Audits** — feature comparison table
9. **Recommendations** — numbered action items with priority
10. **Audit Limitations** — methodology and scope

> Print-friendly CSS included — `Cmd+P` → "Save as PDF" for one-click export.

---

## File Structure

```
mini-program-sast/
├── README.md                    # Bilingual documentation (Chinese + English)
├── README_EN.md                 # English-only documentation
├── SKILL.md                     # Core skill file (agentSkills.io compliant)
└── references/
    ├── rules.md                 # 17 detection rules with grep patterns
    └── audit.sh                 # One-click automated scan script (macOS primary)
```

---

## Compatibility

| AI Tool / Platform | Support |
|--------------------|---------|
| WorkBuddy | ✅ Native |
| Claude Code | ✅ Supported (agentSkills.io) |
| Cursor | ✅ Supported (skills directory) |
| Other agentSkills.io tools | ✅ Universal format |
| CLI (bash) | ✅ `references/audit.sh` (macOS primary) |

### System Dependencies

- `strings` — built-in (macOS / Linux)
- `grep` — built-in
- `find` — built-in
- `bash` — built-in
- **WeChat Desktop Client** (macOS / Windows) — for caching .wxapkg

---

## Comparison with WeChat Official Scanner

| Dimension | Mini Program SAST | WeChat Official Scanner |
|-----------|-------------------|------------------------|
| **Type** | AI-driven static string analysis | Automated scanning service |
| **Speed** | 1 ~ 3 minutes | 5 ~ 30 minutes |
| **Source upload** | Not required (local cache) | Required (miniprogram-ci) |
| **Depth** | String pattern + AI semantics | API call chain analysis |
| **Coverage** | Any locally cached mini program | Only your own uploadable programs |
| **Best for** | Quick triage, 3rd-party audit, emergency checks | Pre-release security gate |

---

## Related: Web SAST

| | Web SAST | Mini Program SAST |
|------|----------|-------------------|
| **Target** | Web pages (H5 / SPA) | WeChat Mini Program .wxapkg |
| **Input** | URL | Mini Program AppID |
| **Method** | HTTP requests + source download | Local cache strings extraction |
| **Rules** | 15 web front-end rules | 17 mini program rules |
| **GitHub** | [web-sast](https://github.com/chen-mingsong/web-sast) | [mini-program-sast](https://github.com/chen-mingsong/mini-program-sast) |

---

## Disclaimer

This tool is for authorized security auditing and self-assessment only. Important notes:

- Third-party mini programs can technically be scanned (if opened in your WeChat), but results should be used for security research and self-assessment only
- Do not exploit discovered vulnerabilities against third-party services
- Report serious vulnerabilities via WeChat Security Response Center (WSRC)
- Comply with local laws and regulations

The author assumes no liability for misuse.

---

## License

MIT License — see [LICENSE](LICENSE) file.

---

## FAQ

**Q: Why is the "opened locally" prerequisite required?**
A: .wxapkg files are downloaded and cached by the WeChat client on first open. The skill cannot download packages directly from WeChat servers (signed downloads), so it relies on local cache. This means you can only scan mini programs you've used.

**Q: How do I fix vulnerabilities after scanning?**
A: .wxapkg files are build artifacts — you cannot modify the local cache. Fixes must be done at the source level in WeChat DevTools before re-uploading. The skill's fix examples are all source-level, ready to copy.

**Q: Will the developer or WeChat detect this scan?**
A: No. The scan is completely offline — it only reads local cache files and sends no network requests to WeChat servers or the mini program backend.

**Q: Can I scan third-party mini programs?**
A: Technically yes, if you've opened them in WeChat. Use for security research and self-assessment only — do not attack third-party services.

**Q: Can it analyze CloudBase configuration?**
A: Init code (`wx.cloud.init`) and environment ID can be extracted if present in the main package. Cloud function source code is stored server-side and cannot be analyzed.
