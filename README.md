<div align="center">

# CitrusGlaze

> **⚠️ Beta Release — This software is experimental. APIs and features may change.**

**The AI security proxy that sees everything your tools are sending.**

210+ secret patterns · 39+ AI tools · 100% local · Zero cloud routing

[Website](https://citrusglaze.dev) · [Report](https://citrusglaze.dev/go/report) · [Scanner](https://github.com/citrusglaze/citrusglaze-scan)

</div>

---

## Install (Beta)

```bash
brew tap citrusglaze/citrusglaze
brew install citrusglaze
```

One command. Setup runs automatically — generates CA cert, configures proxy, starts daemon.

## What It Does

CitrusGlaze sits between your AI tools and the internet. Every request passes through it. Nothing gets out that shouldn't.

| Feature | What |
|---------|------|
| **Secret Detection** | 210+ patterns — AWS keys, API tokens, private keys, DB passwords, connection strings |
| **Secret Blocking** | Critical secrets blocked (HTTP 403). High severity redacted. Low severity logged. |
| **Prompt Injection Scoring** | Detects injection attempts with confidence scoring and evidence |
| **Tool Call Policy** | Block dangerous tool calls (rm -rf, DROP TABLE, credential access) |
| **Cost Tracking** | Per-request token counting and cost attribution across all providers |
| **Shadow AI Discovery** | See which AI tools your team is actually using |

## Supported Tools (39+ verified)

**CLIs:** Claude Code, Codex, Gemini CLI, Aider, GitHub Copilot CLI

**IDEs:** Cursor, Windsurf, VS Code + Copilot, JetBrains AI, Continue.dev

**Desktop:** Claude.app, ChatGPT.app

**Web:** claude.ai, chatgpt.com, gemini.google.com

**SDKs:** OpenAI, Anthropic, Google, AWS Bedrock, Azure OpenAI, LangChain, CrewAI, and all major agent frameworks

**Browsers:** Chrome, Safari, Edge, Arc, Firefox

Full compatibility matrix: [docs/AI-TOOL-PROXY-COMPATIBILITY-MATRIX.md](https://citrusglaze.dev/docs)

## Install

### macOS

```bash
brew install citrusglaze/tap/citrusglaze
```

Or download from [Releases](https://github.com/citrusglaze/citrusglaze/releases).

### Linux

```bash
curl -fsSL https://citrusglaze.dev/install | sh
```

Packages available for Ubuntu/Debian (`.deb`), Fedora/RHEL (`.rpm`), and Arch (AUR).

## Quick Start

```bash
# Start the proxy
citrusglaze start

# Run any AI tool through it
citrusglaze wrap claude
citrusglaze wrap cursor
citrusglaze wrap python my_agent.py

# Check status
citrusglaze status
```

## How It Works

```
[Your AI tools] → [CitrusGlaze proxy :8888] → [AI providers]
                         ↓
                  Scan every request:
                  • Detect secrets (210+ patterns)
                  • Score prompt injection
                  • Enforce tool call policies
                  • Track cost & tokens
                  • Log to local SQLite DB
                         ↓
                  Block / Redact / Allow
```

Your prompts never leave your machine. No cloud routing. No third-party servers.

## Not Sure If You Need This?

Find out what you've already leaked:

```bash
pip3 install citrusglaze-scan && python3 -m citrusglaze_scan
```

Or with [pipx](https://pipx.pypa.io/):

```bash
pipx run citrusglaze-scan
```

Scans your Claude Code, Cursor, and Copilot chat histories for secrets you've already sent to AI providers. Takes 15 seconds. [Source code](https://github.com/citrusglaze/citrusglaze-scan).

## Architecture

- **Detection engine:** Rust (cross-platform, wire-speed, 210+ regex patterns + Shannon entropy)
- **macOS app:** Swift (menu bar UI, system proxy, Keychain integration)
- **Linux daemon:** Rust (headless, systemd service)
- **Proxy:** MITM with per-host TLS certificate generation (ECDSA P-256)
- **Storage:** Local SQLite (write-through, single source of truth)

## Enterprise

For teams that need centralized visibility, SSO, and compliance:

- Cloud dashboard with per-team, per-user, per-tool analytics
- SSO (Google, Microsoft, Okta) — included in base plan, no SSO tax
- Compliance exports (SOC 2, GDPR)
- Alert rules (Slack, PagerDuty, email)

[Contact us](mailto:hello@citrusglaze.dev) · [Pricing](https://citrusglaze.dev/pricing)

## Links

- [State of AI Traffic Report](https://citrusglaze.dev/go/report) — what 26,000+ intercepted AI requests reveal
- [vs Netskope](https://citrusglaze.dev/vs/netskope) · [vs Zscaler](https://citrusglaze.dev/vs/zscaler) · [vs Harmonic](https://citrusglaze.dev/vs/harmonic)
- [Compatibility Matrix](https://citrusglaze.dev/docs) — 39 tools tested and verified

## License

Proprietary. Free for individual use. [Contact us](mailto:hello@citrusglaze.dev) for team and enterprise licensing.
