# CitrusGlaze Privacy Policy

**Last Updated:** March 24, 2026
**Effective Date:** March 24, 2026

CitrusGlaze ("we," "us," or "our") provides AI security and observability software including a desktop application, command-line interface, browser extension, and MCP server (collectively, the "Software"). This Privacy Policy explains what data the Software processes, where it is stored, and what rights you have.

**Important: This is NOT legal advice. Have a qualified attorney review before publishing.**

---

## 1. Our Core Principle: Your Data Stays on Your Machine

CitrusGlaze is designed as local-first software. **All intercepted AI traffic, conversation history, audit logs, and security findings are stored exclusively on your local device.** We do not operate servers that receive, store, or process your AI conversations.

- No cloud accounts required
- No data transmitted to CitrusGlaze servers by default
- No AI model training on your data, ever
- All encryption and processing happens locally

---

## 2. What Data the Software Processes

### 2.1 AI API Traffic (Proxy)

CitrusGlaze operates as a local MITM (Man-in-the-Middle) proxy that intercepts HTTPS connections between your AI tools and AI API providers. This is the core functionality of the product.

**What is intercepted:**
- HTTP request and response headers
- Request bodies (prompts, system prompts, tool calls)
- Response bodies (AI model outputs) — when response capture is enabled
- Target hostname and API path
- Source application identifier (e.g., "claude-code," "cursor")

**What is NOT intercepted:**
- Non-AI traffic (only domains in the configured AI provider list are intercepted)
- Authentication credentials are redacted before storage (API keys, Bearer tokens, cookies)
- Traffic to non-configured domains passes through without inspection

**Where it is stored:** Locally in an encrypted SQLite database on your device at `~/Library/Application Support/CitrusGlaze/citrusglaze.db` (macOS) or `~/.local/share/citrusglaze/citrusglaze.db` (Linux). The database is encrypted at rest using SQLCipher (AES-256-CBC). The encryption key is stored in the macOS Keychain or a local key file with 0600 permissions.

**How long it is retained:** Configurable. Default: 30 days. You can change this in `~/.config/citrusglaze/citrusglaze.toml` via the `storage.retention_days` setting. You can also disable prompt content storage entirely via `storage.store_content = false`.

### 2.2 Browser Extension (CitrusGlaze Lens)

The Chrome extension intercepts fetch and XMLHttpRequest calls on supported AI chat websites to capture conversation data for local security analysis.

**What is captured:**
- POST request bodies to AI chat completion endpoints (e.g., `/backend-api/conversation` on ChatGPT, `/v1/messages` on Claude.ai)
- Response bodies from those endpoints (when content-type is JSON or SSE)

**What is NOT captured:**
- Analytics, telemetry, or tracking requests (filtered by endpoint pattern matching)
- Form data, cookies, or authentication tokens
- Browsing history or activity on non-AI websites
- Data from websites not listed in the extension's `host_permissions`

**Where captured data goes:** Sent to `http://127.0.0.1:8888/api/capture` (localhost only). The data never leaves your machine. If the local proxy is not running, captured data is discarded.

**Chat History Import (Beta):** The extension includes experimental functionality to extract existing conversation history from AI web applications (ChatGPT, Claude.ai, Perplexity, NotebookLM) via DOM content extraction. This feature reads page content using `textContent` only (no `innerHTML`). Extracted data is sent to the local proxy for storage. This feature requires explicit user action (button click or context menu) and does not run automatically.

### 2.3 Desktop Application (Dashboard)

The Tauri desktop application provides a GUI for viewing data already stored in the local database.

**What it accesses:**
- The local SQLite database (read-only for display, read-write for FTS5 index creation)
- The daemon's gRPC server via Unix domain socket (localhost only)

**What it does NOT do:**
- No network requests to external servers
- No analytics, telemetry, or crash reporting
- No auto-update checks (updates are via Homebrew or manual download)

### 2.4 MCP Server (CitrusGlaze Recall)

The MCP server exposes local conversation history to AI tools via the Model Context Protocol (stdio transport).

**What it provides:**
- Read-only access to the local prompts database
- Full-text search across stored conversations
- Agent listing and conversation detail retrieval

**Security:**
- stdio transport only — no network listener, no HTTP endpoint
- Read-only database access (`SQLITE_OPEN_READ_ONLY`)
- Process-level isolation (parent-child relationship with the MCP client)
- No authentication required (implicit via Unix process model)

---

## 3. Optional Telemetry (Disabled by Default)

CitrusGlaze includes an optional telemetry system that sends **aggregate pipeline metadata** to Tinybird for product analytics. This is **disabled by default** and must be explicitly enabled by the user.

**If enabled, what is sent:**
- Event timestamp and unique request ID
- Source application name and target host
- Pipeline action (allow, block, modify, downgrade)
- Finding counts and severity levels
- Pipeline latency
- Anonymous user identifier (macOS username hash) and device identifier (hardware UUID hash)

**What is NEVER sent, even with telemetry enabled:**
- Prompt content, system prompts, or AI responses
- API keys, tokens, or credentials
- File paths, code snippets, or intellectual property
- Personally identifiable information (names, emails, addresses)
- Raw HTTP request or response bodies

**How to enable/disable:**
```toml
# ~/.config/citrusglaze/citrusglaze.toml
[storage.telemetry]
enabled = false  # default
```

---

## 4. Data We Collect About You (Not Your AI Traffic)

### 4.1 Homebrew / Download

When you install via Homebrew or download from GitHub Releases, GitHub may log your IP address per their privacy policy. We do not operate any download servers.

### 4.2 Chrome Web Store

When you install the Chrome extension from the Chrome Web Store, Google collects installation data per their privacy policy. We do not have access to individual installation data.

### 4.3 Website

Our website at citrusglaze.dev may use privacy-respecting analytics (no cookies, no personal data). See the website's separate privacy notice.

---

## 5. Data Security

| Measure | Implementation |
|---------|----------------|
| Encryption at rest | SQLCipher AES-256-CBC for all stored data |
| Encryption key storage | macOS Keychain (preferred) or 0600-permission local file |
| File permissions | Database file set to 0600 (owner read/write only) |
| gRPC authentication | Unix domain socket with peer UID verification — rejects cross-user connections |
| Audit trail integrity | SHA-256 chain hashing on audit events — tamper detection |
| Credential redaction | API keys, Bearer tokens, and cookies are redacted before any storage |
| Network isolation | All inter-component communication via localhost (Unix socket or 127.0.0.1) |
| PII redaction option | `storage.store_content = false` replaces all prompt content with `[redacted]` |

---

## 6. Data Sharing

**We do not sell, rent, license, or share your data with any third party.**

The Software does not transmit intercepted AI traffic, conversation history, audit logs, or security findings to any external server unless you explicitly enable the optional telemetry feature (Section 3), which sends only aggregate metadata.

Data may be disclosed only:
- **At your direction** — e.g., you export the audit log and share it
- **To comply with law** — if compelled by valid legal process (court order, subpoena)
- **To prevent harm** — if necessary to prevent death or serious physical harm

---

## 7. Your Rights

### 7.1 Access and Portability

All your data is stored locally on your device in a standard SQLite database. You can:
- Query it directly: `sqlite3 ~/Library/Application\ Support/CitrusGlaze/citrusglaze.db`
- Export audit logs as CSV from the Dashboard
- Search all conversations via the Remembrall search or MCP server

### 7.2 Deletion

- **Delete specific data:** Remove individual records via SQL
- **Delete all data:** Run `citrusglaze uninstall` or delete the database file
- **Automatic deletion:** Configure `storage.retention_days` for automatic purging (default: 30 days)

### 7.3 Disable Collection

- **Stop intercepting:** Run `citrusglaze stop` to disable the proxy
- **Disable content storage:** Set `storage.store_content = false` to store only metadata
- **Uninstall extension:** Remove from `chrome://extensions`
- **Full removal:** Run `citrusglaze-brew-cleanup && brew uninstall citrusglaze`

### 7.4 GDPR Rights (EU Users)

If you are in the European Economic Area, you have additional rights under GDPR including the right to access, rectification, erasure, restriction of processing, data portability, and objection. Since all data is stored locally on your device and we do not process it on our servers, you exercise these rights directly through the Software. Contact us at privacy@citrusglaze.dev for any GDPR-related inquiries.

### 7.5 CCPA Rights (California Users)

California residents have the right to know what personal information is collected, request deletion, and opt out of the sale of personal information. We do not sell personal information. All data is stored locally. Contact us at privacy@citrusglaze.dev for CCPA-related requests.

---

## 8. Children's Privacy

The Software is not directed at children under 13. We do not knowingly collect data from children. If you believe a child has used the Software, the data exists only on the device where it was installed and can be deleted by removing the application.

---

## 9. Disclaimer of Liability

THE SOFTWARE IS PROVIDED "AS IS" AND "AS AVAILABLE" WITHOUT WARRANTIES OF ANY KIND, EXPRESS OR IMPLIED, INCLUDING BUT NOT LIMITED TO WARRANTIES OF MERCHANTABILITY, FITNESS FOR A PARTICULAR PURPOSE, AND NON-INFRINGEMENT.

TO THE MAXIMUM EXTENT PERMITTED BY APPLICABLE LAW:

(a) IN NO EVENT SHALL CITRUSGLAZE, ITS OFFICERS, DIRECTORS, EMPLOYEES, OR AGENTS BE LIABLE FOR ANY INDIRECT, INCIDENTAL, SPECIAL, CONSEQUENTIAL, OR PUNITIVE DAMAGES, INCLUDING WITHOUT LIMITATION LOSS OF PROFITS, DATA, USE, GOODWILL, OR OTHER INTANGIBLE LOSSES, RESULTING FROM (i) YOUR ACCESS TO OR USE OF OR INABILITY TO ACCESS OR USE THE SOFTWARE; (ii) ANY CONDUCT OR CONTENT OF ANY THIRD PARTY, INCLUDING AI PROVIDERS WHOSE TRAFFIC IS INTERCEPTED; (iii) ANY CONTENT OBTAINED FROM AI SERVICES THROUGH THE SOFTWARE; OR (iv) UNAUTHORIZED ACCESS, USE, OR ALTERATION OF YOUR DATA.

(b) CITRUSGLAZE'S TOTAL AGGREGATE LIABILITY SHALL NOT EXCEED THE AMOUNT YOU PAID FOR THE SOFTWARE IN THE TWELVE (12) MONTHS PRECEDING THE CLAIM, OR ONE HUNDRED DOLLARS ($100), WHICHEVER IS GREATER.

(c) CITRUSGLAZE IS NOT RESPONSIBLE FOR THE CONTENT, ACCURACY, OR LEGALITY OF AI-GENERATED RESPONSES INTERCEPTED BY THE SOFTWARE. THE SOFTWARE PROVIDES SECURITY MONITORING AND OBSERVABILITY — IT DOES NOT GUARANTEE DETECTION OF ALL THREATS.

(d) THE INTERCEPTION OF NETWORK TRAFFIC BY THE SOFTWARE IS PERFORMED AT YOUR DIRECTION AND ON YOUR DEVICES. YOU ARE RESPONSIBLE FOR ENSURING COMPLIANCE WITH APPLICABLE LAWS REGARDING NETWORK MONITORING IN YOUR JURISDICTION.

---

## 10. Indemnification

You agree to defend, indemnify, and hold harmless CitrusGlaze and its officers, directors, employees, and agents from any claims, damages, obligations, losses, liabilities, costs, or expenses arising from: (a) your use of the Software; (b) your violation of this Privacy Policy or any applicable law; (c) your use of intercepted data; or (d) your failure to secure the local database containing intercepted data.

---

## 11. Governing Law and Dispute Resolution

This Privacy Policy is governed by the laws of the State of Delaware, United States, without regard to conflict of law principles. Any disputes shall be resolved through binding arbitration administered by JAMS under its Streamlined Arbitration Rules, conducted in English, with the seat of arbitration in Wilmington, Delaware. The arbitrator's decision shall be final and binding. You waive any right to participate in a class action lawsuit or class-wide arbitration.

---

## 12. Changes to This Policy

We may update this Privacy Policy from time to time. We will notify you of material changes by updating the "Last Updated" date and, if applicable, through the Software's update mechanism. Your continued use of the Software after changes constitutes acceptance of the updated policy.

---

## 13. Contact

For privacy-related questions or requests:

- **Email:** privacy@citrusglaze.dev
- **GitHub:** https://github.com/citrusglaze/citrusglaze/issues

---

## 14. Summary Table

| Data Type | Collected? | Stored Where | Shared? | Retention |
|-----------|-----------|-------------|---------|-----------|
| AI prompts & responses | Yes (local proxy) | Local encrypted DB | Never | 30 days (configurable) |
| API keys & credentials | Redacted before storage | Never stored | Never | N/A |
| Source app identity | Yes | Local encrypted DB | Never | 30 days |
| Security findings | Yes | Local encrypted DB | Never | 30 days |
| Telemetry metadata | Only if opted in | Tinybird (if enabled) | Tinybird only | Per Tinybird policy |
| Browsing history | Never | N/A | Never | N/A |
| Personal information | Never | N/A | Never | N/A |
