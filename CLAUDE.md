# Project context for Claude Code

This repository is **documentation only**. It contains no product code. It builds
the NubeStack OpsPilot product documentation site with MkDocs + Material and
publishes it to GitHub Pages.

Read this file and `CONTRIBUTING.md` before editing any page. `CONTRIBUTING.md`
holds the writing style rules; this file holds the facts, the sources and the
invariants.

## What the product is

NubeStack OpsPilot is a **desktop operations workbench** — an Electron app for
Windows, macOS and Linux. It is a terminal, a file manager, a remote-desktop
client and an AI assistant in one window, built around one idea:

> The AI helps you operate your infrastructure. It never operates your
> infrastructure itself.

The audience is DevOps, SRE, network, platform and development engineers, with a
specific pitch to regulated and disconnected estates (banking, defence,
healthcare, utilities, industrial control, air-gapped networks).

Current product version: **0.1.0**.

## The product repository

The product lives in a **separate** repository, checked out alongside this one at
`../opspilot` during authoring. It is not a submodule and is not vendored here,
so it may be absent. When it is present, it is the source of truth; when it is
absent, do not invent facts to fill a gap — leave the gap and say so.

### Canonical sources, in precedence order

1. **`../opspilot/` source code** — the highest authority. If the code and a
   document disagree, the code wins and the document is stale.
2. **`../opspilot/NubeStack-OpsPilot-User-Guide.md`** — a 1,240-line product and
   user guide, version 0.1.0. This site's nav was derived from its 22 chapters,
   and most pages here have a direct counterpart section in it. Start here for
   narrative, positioning and wording.
3. **`../opspilot/docs/OpsPilot-Security-Model.md`** — the threat model, the
   redaction rationale, credential storage, audit logging.
4. **`../opspilot/docs/OpsPilot-Provider-And-Workflow-Architecture.md`** — the
   provider adapter interface, the SFTP/FTP plan, the host/session scope
   hierarchy.
5. **`../opspilot/README.md` and `../opspilot/CLAUDE.md`** — **treat with
   caution.** Both still describe the early MVP skeleton (single SSH session,
   three providers, no keychain) and are substantially out of date relative to
   the shipped 0.1.0 product. Use them for build/packaging detail only, and
   verify anything else against the source.

### Where specific facts live in the source

| Fact | File |
|---|---|
| The ten AI providers, models, auth flows, base URLs | `src/settings-overlay.js` (`AI_PROVIDERS`) |
| The ten connection types and which form fields each shows | `src/connection-types.js` (`CONNECTION_TYPES`) |
| Default dangerous-command patterns | `src/command-safety-defaults.js` |
| The ten redaction categories and their defaults | `src/redaction-defaults.js` |
| Redaction implementation, the single choke point | `src/redactor.js`, `ai:analyze` in `main.js` |
| Custom-pattern validation (worker thread + timeout) | `src/redaction-validator.js`, `src/redaction-validator-worker.js` |
| MCP tools exposed to assistants | `src/mcp-server.js` (`registerTool` calls) |
| Assistant config writers | `src/claude-desktop-config.js`, `src/vscode-mcp-config.js`, `src/chatgpt-desktop-config.js` |
| The ChatGPT tunnel, localhost-only enforcement | `src/openai-mcp-tunnel.js` |
| Hypervisor console backends (KVM/libvirt, OpenStack Nova) | `src/hypervisors/` |
| RDP embedding and the platform difference | `src/rdp-embed.js`, `src/rdp-embed-mac.js`, `../opspilot/CLAUDE.md` |
| Profile stores and resolution precedence | `src/command-safety-profile-store.js`, `src/redaction-profile-store.js` |
| Connections, groups, environments | `src/connection-store.js`, `src/group-store.js`, `src/environment-store.js` |
| Installers, targets, packaging | `../opspilot/package.json` (`build` block) |
| Risk-tier classification, approval flow | `renderer.js` (`matchDangerousPattern`, `resolveEffectiveDangerousPatterns`, `handleApprove`) |
| Verification scripts worth reading for behaviour | `../opspilot/scripts/verify-*.js` |

### Known inaccuracies in the user guide

The guide is the best narrative source but it is not perfectly faithful to the
shipped UI. These were verified against the source during the initial authoring
pass. **Follow the right-hand column.** If you find another, add it here.

This section exists so the documentation stays correct, and only for that. It is
not a defect log. Where the product's own behaviour looks wrong rather than
merely undocumented, raise it with NubeStack engineering directly; do not record
it here and do not write it into a page. This file stays focused on what a
documentation author needs in order to write accurate pages.

| The guide says | The product actually does |
|---|---|
| The connection field is **AI enabled** | The dialog label is **Enable AI**, with the sub-caption "AI panel for this host" |
| "Click **Save**, then double-click the connection to connect" | There is no Save button. There is a **Save connection** toggle (captioned "Credentials encrypted, never plaintext") and a **connect** button. Double-clicking a saved row connects it thereafter (`renderer.js`, `openSavedConnection`) |
| Auto-run and dangerous confirmation are under **Settings → Security** | They are under a **Command Safety** group on that page: **Settings → Security → Command Safety**. A code comment in `src/settings-overlay.js` records that auto-run was deliberately moved there out of AI Features |
| (not mentioned) | The connection dialog also has **Let AI Assistant open this session** — off by default and additionally gated by a master switch in **Settings → AI Assistants**. It is a separate, stricter permission from **Enable AI** |
| Quickstart tells the reader to add `rm -rf`, `drop table`, `mkfs`, `dd if=`, `shutdown`, `terraform destroy` to the dangerous list | Five of those six already ship in the 21-entry default list in `src/command-safety-defaults.js`. Only `terraform destroy` is genuinely absent. The reader's job is estate-specific additions, not the obvious ones |

More, all verified in source during the initial pass:

| The guide says | The product actually does |
|---|---|
| "Production, staging and **development** ship by default" (§9.3) | `src/environment-store.js` `DEFAULTS` seeds exactly **two**, production and staging, and the dialog `<select>` has exactly those two options |
| `ollama pull llama3` (§8, §12.2) | There is **no plain `llama3` tag** in the Ollama picker, and Ollama is not `allowCustomModel`, so a user who follows the guide cannot select what they pulled. `testConnection()` compares the selection against `/api/tags` and fails with "Model … not found". Use **`llama3.3`**, which is what the in-app `consoleGuide` says. (`OllamaProvider`'s constructor default is still the stale `'llama3'`) |
| "Click **Approve**" (§8) | The real button labels in `renderer.js` are lowercase **approve & run** and **dismiss** |
| The connection type is "FTP / SFTP" (§9.1) | **There is no SFTP connection type.** Only `ftp` exists. SFTP is reached through an SSH session's file explorer, not as a connection you create |
| VNC "Opens: Embedded desktop" unconditionally (§9.1) | Only the two **hypervisor** targets are embedded (noVNC). **Direct VNC host** calls `launchExternalSession` and opens the system VNC viewer |
| RSH has port 514 (implying it is configurable) | `hasPort: false` — the dialog shows **no port field** for RSH |
| `list_connections` needs no approval and is read-only metadata (§13.2) | It returns `isError: true` unless `cfg.allowSessionOpen` is on, so with session-opening off an assistant sees **no connection list at all**. Stricter than documented, but a real behavioural difference |
| Azure OpenAI is just "your own tenancy and deployment" (§2.6, §12.3) | The catalogue also has `deviceFlow: { provider: 'azure' }` — a Microsoft identity platform device authorisation grant that works with any Azure AD tenant — *as well as* an API-key field |

**The `Enable AI` default contradicts the guide, and this needed a decision.**
Guide §8 says AI is "off until you enable it", and that is how the product is
positioned. But `openNewConnectionDialog()` and `closeDialog()` both call
`setConnAiEnabled(true)`, and the module-level default is `connAiEnabled = true`,
so **the new-connection dialog opens with Enable AI switched on**. The decision
taken: pages do **not** assert a default state in either direction. They tell the
reader to set the toggle deliberately and check it before connecting, and they
state the invariant's real substance — AI off means invisible to every model and
assistant — which the code does uphold. Two genuine off-by-default facts you
*can* state: the seeded **Local** connection is `aiEnabled: false`, and
**Let AI Assistant open this session** is off by default (`connAiCanOpen = false`).

**Two other stale-flag traps in `connection-types.js`.** RDP and VNC are both
`inApp: false, isExternal: true`, and RDP's `desc` still says it "Launches
mstsc.exe on Windows"; VNC's says "Opens your system VNC viewer". Neither
matches shipped behaviour — `renderer.js` routes Windows RDP to
`openRdpInWindow` (a real in-app tab) and hypervisor VNC to an embedded noVNC
tab. **The flags remain authoritative for which form fields appear**, which is
all they drive; do not quote the stale `desc` text for those two.

**`AI_PROVIDERS` is internally inconsistent about Anthropic models.** The entry's
`tagline` advertises `claude-opus-4` but its `models` array contains
`claude-opus-4-8`, and `AnthropicProvider`'s constructor default is
`claude-sonnet-4-6`. Prefer not to quote specific Anthropic model names; point at
the app's own model picker.

**Do not tell readers to read their version off the interface.** The released
version is **0.1.0** (`package.json`). Where a page needs a reader to report their
version, tell them to take it from the download or installer they ran.

**Application data paths are not printable.** Every store uses
`app.getPath('userData')` with no literal path. `src/mcp-stdio-bridge.js` has a
per-platform mapping using the internal app name `opspilot-mvp`, but that is the
dev `package.json` `name` and is not confirmed for packaged 0.1.0 builds. Do not
print a path; describe how to find the folder (the one holding `connections.json`
and `settings.json` side by side) instead. The store filenames themselves *are*
verified: `connections.json`, `groups.json`, `environments.json`,
`command-safety-profiles.json`, `redaction-profiles.json`, `settings.json`,
`transfer-history.json`, `credentials.enc.json`, and
`openai-tunnel/runtime-key.enc`.

**Verified numbers and behaviours worth reusing.** Proposal timeout is
`PROPOSAL_TIMEOUT_MS = 5 * 60 * 1000`; `open_session` has a separate
`OPEN_TIMEOUT_MS = 20 * 1000`. Tunnel ID pattern is
`/^tunnel_[A-Za-z0-9_-]{8,}$/`. `validateLocalMcpUrl()` requires `http:`, host
exactly `127.0.0.1`, path exactly `/mcp`, and a `token` query parameter, and the
tunnel client's health listener is pinned to `127.0.0.1:0`. The scrollback window
sent for analysis is 8,000 characters (`getRecentOutput`); the attachment cap is
40,000 (`AI_ATTACH_MAX_CHARS`), and a **local** file attachment passes
`connectionId: undefined` so it always resolves to the **Default** Data Handling
profile. The UI field is labelled **Tunnel ID**, though surrounding copy says
"OpenAI tunnel ID".

**Provider API keys and connection credentials are stored differently.** Get
this right on every page that touches it, and do not describe the two as though
they share one mechanism. In the code:

| Secret | Where it actually goes |
|---|---|
| SSH passwords, private keys, passphrases; OpenStack passwords and application-credential secrets; the S3 secret access key | Encrypted. Electron `safeStorage` → `credentials.enc.json` (`src/connection-store.js`), which **refuses to save rather than downgrading to plaintext** if `safeStorage.isEncryptionAvailable()` is false |
| **AI provider API keys** | **Plain text** in `settings.json`. `src/settings-overlay.js` → `settings:set` (`main.js:2374`) → `saveSettings()` (`main.js:124`), which is a bare `fs.writeFileSync(..., JSON.stringify(...), 'utf8')`. No `safeStorage` on that path |
| OpenAI tunnel runtime key | Encrypted, in its own file (`openai-tunnel/runtime-key.enc`), decrypted only into the tunnel child process |

Say which is which. The Security page's **Credential storage: OS Keychain**
badge describes the connection-credential path; do not extend it to provider API
keys. Keep the published wording factual about where each secret lives, and point
readers who need inference credentials held to the same standard at a local or
self-hosted model.

**The Clear button's scope.** Its handler (`src/settings-overlay.js:1881`)
clears `aiProviders` and `activeProvider`. Document that verified scope rather
than the broader wording on the button's confirmation prompt.

**Auto-run is application-wide, not per environment.** It is stored as
`aiFeatures.autoRunSafeCommands` — a single workstation switch. The guide's
"scope it per environment" is misleading; per-environment *policy* is achieved
through profiles, and auto-run is not part of a profile. Relatedly, the
pending-approval taskbar/dock badge **does not exist on Linux** (Electron
exposes neither API), and foreground-raising happens when the user clicks the
notification rather than automatically.

**The destructive-command gate is a typed free-text justification of at least
ten characters** (`DANGEROUS_REASON_MIN_LEN = 10` in `renderer.js`), not a fixed
confirmation phrase. The security model document's §7 "type a confirmation
phrase" is intent, and **the product's own About page repeats the same error** in
its "Security guarantees" list. Do not document typing `RUN`. Two further
precisions the guide's §20.5 table gets wrong:

- It is **one `confirm & run` button** that enables once the justification
  crosses ten characters — not a typed reason followed by a separate click.
- The typed reason is **conditional** on the resolved profile's *Require a
  written reason for dangerous commands* setting. With that off, a High risk
  command needs an explicit click and no reason. **The click can never be
  turned off; the reason can.**

**`propose_command`'s schema is bigger than the guide says.** Guide §20.1 lists
`sessionId` and `command`. The real `inputSchema` also has a **required**
`reason` (string) plus optional `note` (string), `safe` (boolean) and
`dangerous` (boolean). The two booleans are the model's own self-assessment and
are load-bearing for the risk tier. `open_session` has its own separate
`OPEN_TIMEOUT_MS = 20 * 1000`, deliberately distinct from the proposal timeout
because there is no human to wait for, only the connect handshake.

**`DEFAULT_MCP_PORT = 8765`** — absent from the guide entirely, though §20.2 is
meant to serve a firewall reviewer.

**`isExternal` and `isFileBrowser` do not route the surface — they gate the AI
toggle.** Their only consumers in `renderer.js` (lines ~674 and ~9508) decide
whether **Enable AI** is offered. Do not document them as "how the connection
opens". Note also a stale comment at ~9511 claiming "telnet/rsh DO show that
toggle" while the condition above it hides the AI row for both: **Enable AI is
available for SSH and serial only.**

**`src/menubar.js`'s `shortcut:` strings are display labels, not bindings.** The
file registers no accelerators, and nothing in the repo calls
`globalShortcut.register` except a conditional `F11` for focused RDP sessions.
**New Session** is labelled `Ctrl+N` in two menus and no handler anywhere
responds to `Ctrl+N` — the implemented new-connection shortcut is `Ctrl+T`.
Treat every menu `shortcut:` string as unverified until you find a handler.

**Redaction does not hash-and-map hostnames or IPs.** The security model
document describes hash-and-map so the AI could still reason about "host A
talked to host B". `src/redactor.js` does not do this — matches become
`[REDACTED:<category>]`, so relationships between hosts are not preserved.

**Credential handling, confirmed good and worth documenting.**
`src/connection-store.js` **refuses to save rather than downgrading to
plaintext** when `safeStorage.isEncryptionAvailable()` is false. OpenStack
passwords and application-credential secrets use the same encrypted path. The S3
secret access key is stored in the connection's `password` slot. But note one
real exposure: **on Linux an RDP password is passed to `xfreerdp` on the command
line**, so it is visible to anything that can list the user's processes. Document
that plainly; do not hide it.

**No SSH host key verification ships in 0.1.0.** A grep for
`hostkey|fingerprint|known_hosts|hostVerifier` across `src/` and the root JS
finds nothing on the SSH path. The security model document's §8 bullet is intent.
Say so, and tell readers not to plan a control around it.

**Other real limits found in source.** `Force fresh session before connecting` is
Windows-only and destroys unsaved work in the session it logs off. Per-connection
Command Safety and Data Handling profile assignment is **SSH-only**, and the AI
badge is not rendered for external launchers, file browsers, Telnet or RSH —
`ai:analyze` resolves sessions through `sshManager.getSession` specifically, so
Telnet and RSH can never use AI even if the flag was saved as on. `src/themes/`
contains exactly one theme and `ThemeManager` exposes no selector, so adding a
theme is a source-level change. `src/term-search.js` tooltips advertise
`Alt+C`/`Alt+W`/`Alt+R` but **none of those bindings is implemented** — they are
clickable buttons only. System Monitor collects no load average despite the
guide's claim, so do not document a load figure. The Insights resource section
reads different key names from the ones System Monitor emits, so describe it as
bars built from the most recent monitor sample rather than promising specific
metrics.

**RDP per platform — the guide and the product's own `CLAUDE.md` are both
wrong about macOS.** Both say macOS and Linux launch FreeRDP (`xfreerdp`) as an
external window. The shipped code does this instead:

| Platform | What actually happens |
|---|---|
| Windows | Embedded in-app tab (`src/rdp-embed.js`, koffi + Win32 `SetParent`) |
| macOS | `main.js` writes a standard `.rdp` file and hands it to Microsoft's **Windows App** (`open -a "Windows App"`) — chosen because it beat every FreeRDP-on-macOS path on quality and on a stuck-session case. The password is deliberately never written into the generated `.rdp` file |
| Linux | `xfreerdp` / `xfreerdp3` as an external window |

`src/rdp-embed-mac.js` implements a third, **unused** strategy (an
`sdl-freerdp` docked pane tracked via AppleScript, needing Accessibility
permission), reachable via the `rdpwin:open` IPC handler but never routed to by
`renderer.js`. Do not present it as the macOS experience. Note also that
`vendor/freerdp` is scoped to `win.extraResources` in `package.json`, so "RDP
bundles FreeRDP, nothing extra to install" is a **Windows** statement.

**Redaction is one choke point with six call sites, not one handler.** The
product's `CLAUDE.md` says "exactly one path … (the `ai:analyze` handler)".
`src/redactor.js`'s own header comment enumerates six: `ai:analyze`, `ai:ask`,
`cmd:approve`, attachment preparation, and `mcp-server.js`'s `get_recent_output`
and `read_file`. The invariant holds — one entry point, always redacting first —
but describe it as a single choke point used by every AI path, not as a single
handler.

**The provider adapter interface has a streaming variant.** The documented
interface is `testConnection()` / `sendDiagnosis(redactedContext)`, but the real
work is in `sendDiagnosisStream(redactedContext, onChunk, signal)`;
`sendDiagnosis()` is that call with no callback. The streaming variant is what
makes the Stop button and `ai:cancel` possible.

**Redaction category labels: use the code's, not the guide's.** The code says
"Private key blocks", "Bearer tokens", "JWTs", "Password/secret assignments",
"UUIDs"; the guide writes "PEM private key blocks", "HTTP bearer tokens", "JSON
Web Tokens", "UUIDs and GUIDs". The five-on / five-off split does match.

**Both `../opspilot/docs/*.md` design documents are pre-implementation and
partly wrong about the shipped product.** The provider architecture document
lists AWS Bedrock among five platforms and recommends wiring only two adapters;
the shipped catalogue is ten providers over three adapter classes, with **no
Bedrock**. The security model document describes per-connection "Standard /
Regulated / Air-gapped" sensitivity tiers, host and session right-click
overrides, and an encrypted local session/audit log with exportable
transcripts — **none of which matches the shipped model**, which is named Data
Handling and Command Safety *profiles* resolving connection → group → Default,
with scrollback that is memory-only and never written to disk. Take the threat
model reasoning and the credential-storage facts from that document; take
nothing from it about tiers or audit logging without confirming it in the code.

Two further facts worth knowing, neither contradicted by the guide because the
guide does not mention them:

- `package.json` has **no `engines` field**, so there is no authoritative Node
  version for building from source. Say "a current LTS release", not a number.
- macOS builds are configured with `hardenedRuntime: false` and
  `gatekeeperAssess: false`, so **they are not notarised as configured**. Do not
  imply otherwise.

## Product invariants — never contradict these in a page

These are architectural properties of the product, not settings. Writing
anything that implies otherwise is a correctness bug in the documentation.

1. **The AI is never given a shell.** A model's output is a *proposal*. It
   becomes a real command only through an explicit approval event raised by a
   user action. No setting, provider, assistant, prompt or flag changes this.
   The UI reports it as **Always enforced**, not as a toggle.
2. **Redaction happens before egress, on one code path.** Terminal output
   reaches no AI provider without passing through the local redactor first.
   There is exactly one path from the terminal buffer to AI context.
3. **The dangerous list can only get stricter.** A pattern match can promote a
   command to High risk; nothing can demote a command the model already flagged.
   State the asymmetry whenever risk classification comes up.
4. **Dangerous patterns are plain, case-insensitive substrings, not regexes.**
   This is deliberate. Custom *redaction* patterns, by contrast, are real
   regexes — and those are validated in a worker thread with a timeout.
5. **Auto-run ships off.** Read-only auto-run is genuinely useful and genuinely
   optional. Never present it as a default.
6. **AI access is per connection, and a connection with AI off is invisible to
   every model and every assistant.** That off-state semantics is the invariant
   and the code upholds it. **Do not claim it defaults to off** — see the note on
   the `Enable AI` default below, which contradicts the guide.
7. **Profiles resolve connection → group → Default, and are replacements, not
   additions.** A group profile can legitimately be *less* strict than Default.
8. **The MCP connector binds to `127.0.0.1` only.** No public listener is ever
   opened, including in the ChatGPT tunnel case.
9. **No telemetry, no account system, no cloud backend.** Settings, connections
   and profiles are local to the workstation. Terminal scrollback is memory-only:
   OpsPilot never writes it to disk of its own accord. The only exceptions are
   explicit, user-initiated exports — **Save terminal output** and **Print
   terminal output** in the tab context menu. State it that way rather than as an
   absolute, and keep `safety/` and `workspace/` consistent on it.
10. **"Air-gapped" requires a local model.** With a cloud provider the
    *workstation* still reaches the internet — that is **private / VPN-only**
    operation. Keep the two terms distinct; the product is careful about this and
    so is this documentation.

## Honesty rules

The product guide's own principle is *"say what is true — limitations are
documented rather than marketed around."* This site inherits that.

- State a boundary once, in the place the reader needs it, and in the positive
  where possible. Remote operation through the tunnel covers triage rather than
  destructive change. Small local models are less precise at diagnosis. RDP
  certificate handling is provisional ahead of GA. Telnet and RSH are
  unencrypted. See rule 5 in `CONTRIBUTING.md` for how to phrase these — do not
  catalogue them, and do not apologise for them.
- Never invent a number, a price, a version, a keyboard shortcut, a CLI flag, a
  file path, a URL or a support address. If you cannot find it in the sources,
  omit it or mark it explicitly as not yet documented.
- Do not add a competitor comparison table, a customer name, a case study, a
  certification claim (SOC 2, ISO 27001, FedRAMP, HIPAA compliance) or a
  performance benchmark. None of those are substantiated by the sources.
- Third-party facts about an AI provider's data retention change over time. The
  security-model source records some as of mid-2026; attribute them rather than
  stating them flatly, and point readers at the provider's own terms.

## Known gaps — questions the sources do not answer

These came out of the initial authoring pass. Readers will plausibly ask all of
them and **no source answers any of them**, so every page that brushes against
one either omits it or routes the reader to the NubeStack channel. Do not fill
any of these in from assumption. If you get an authoritative answer from
NubeStack, document it and delete the line here.

**Commercial**

- How to actually buy. There is no purchase URL, checkout or billing portal in
  any source.
- Currency and tax. `$5` is stated with no currency qualifier; nothing on VAT or
  sales tax. Do not assert USD.
- Annual pricing, volume or team discounts, enterprise plans.
- Cancellation, refunds, and what happens mid-cycle. The guide describes what
  happens after the *trial* ends, but not what happens if an active
  subscription lapses.
- What "per user, per month" means as a licence unit — named versus concurrent
  user, or multiple workstations per user.
- **Licence key and activation mechanics.** Nothing describes how a subscription
  is applied to an installation, whether there is an activation step, or whether
  it requires network access. Given the air-gapped pitch, this is the largest
  practical gap in the documentation.
- What event starts the 15-day trial — install, first run, or first AI use.

**Legal and compliance**

- The EULA, commercial licence terms and third-party notices are "supplied with
  your subscription", so a legal or security reviewer cannot read them from this
  site. Do not paraphrase terms you have not seen.
- No security contact, PGP key, disclosure timeline or bounty for vulnerability
  reporting beyond "your support channel".
- No data processing agreement, sub-processor list or GDPR-style documentation,
  despite the regulated-estate positioning.

**Support and releases**

- No support hours, response targets, severity definitions or tiers.
- No release date for 0.1.0, no release cadence, no per-version support window.

**Product**

- Whether the local encrypted audit log ever ships. The security-model document
  treats it as MVP scope; nothing in the source confirms it in 0.1.0, so
  `about/release-notes.md` records under known limitations that this release
  writes no durable audit log, and `safety/security-model.md` tells readers to
  capture an execution record from their own session-recording arrangements.
  **Verify against `../opspilot/src/` before GA** — if it does ship, both of
  those need updating.

## Build and check

```bash
python3 -m venv .venv && . .venv/bin/activate
pip install -r requirements.txt
mkdocs serve          # http://127.0.0.1:8000
mkdocs build --strict  # what CI runs — must pass
```

CI (`.github/workflows/docs.yml`) runs `mkdocs build --strict` on every push and
pull request, and deploys `main` to GitHub Pages. `--strict` fails the build on a
broken internal link or a page missing from the nav, so:

- Every page in `docs/` must appear in the `nav` in `mkdocs.yml`.
- Every internal link must be a working relative path to a real `.md` file.
- Adding a page means adding a nav entry in the same change.

## Layout

```
mkdocs.yml                    theme, markdown extensions, nav
requirements.txt              pinned mkdocs + material + pymdown-extensions
docs/
  index.md                    site landing page
  overview/ getting-started/ connections/ workspace/ ai/
  safety/ operations/ reference/ about/
  assets/images/              product screenshots, numbered 00-13
  stylesheets/extra.css       risk-tier badges, screenshot and caption styling
.github/workflows/docs.yml    strict build + Pages deploy
CONTRIBUTING.md               writing style, page conventions, review checklist
```

Screenshots are copied from `../opspilot/docs/images/` and keep their numbering,
which skips `06`. Do not renumber them; the numbers are how the product repo
refers to them too.

## Style

Prose, not marketing. British spelling. Sentence case for headings. See
`CONTRIBUTING.md` for the full set of conventions, including the risk-tier badge
markup, image and caption conventions, and admonition usage.
