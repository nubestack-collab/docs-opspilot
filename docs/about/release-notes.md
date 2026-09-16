# Release notes

Release history for NubeStack OpsPilot. The current version is **0.1.0**, and it is the
first thing to include when you [contact support](support.md).

!!! note "Convention for future releases"
    New releases are appended **above** 0.1.0, newest first: add a `## <version>`
    section at the top of the list below, keep the same subheadings (added, changed,
    fixed, known limitations) and only use the ones that apply.

## 0.1.0

First packaged release of NubeStack OpsPilot, with installers for Windows, macOS and
Linux.

### Added

- Multi-session terminal workspace across ten connection types.
- Ten AI providers, including local Ollama and any OpenAI-compatible endpoint.
- MCP connector for Claude Desktop, VS Code Copilot Chat, ChatGPT Desktop and ChatGPT
  Web/Work.
- Secure tunnel for browser and mobile ChatGPT access.
- Approval-gated command execution with three risk tiers.
- Command Safety profiles, assignable per connection and per group.
- Data Handling redaction profiles with ten built-in categories and validated custom
  patterns.
- Encrypted credential storage for SSH and the tunnel runtime key.
- Embedded RDP and VNC, SFTP/FTP file management, S3 browsing.
- Port forwarding, snippets, system monitor, Monaco-based file editing.
- Idle lock and session lock policy.

### Known limitations

- RDP certificate handling is provisional ahead of GA; see
  [Remote desktop](../connections/remote-desktop.md).
- RDP is an embedded in-app tab on Windows and an external client window on macOS and
  Linux — Microsoft's Windows App on macOS, FreeRDP on Linux.
- Telnet and RSH are unencrypted by protocol design, so credentials and session content
  travel in clear text.
- Approvals and proposals are visible in a session while it is open, and terminal
  scrollback is memory-only, so this release writes no durable audit log; capture
  compliance evidence from your own session-recording arrangements.
- Remote operation through the tunnel covers triage — service status, logs, confirming
  an alert — and a High risk command still requires a typed justification at the
  workstation.

## See also

- [Plans & subscription](plans.md) — the trial, the price and what the subscription
  includes
- [Support](support.md) — what to include when you report a problem
- [Troubleshooting](../operations/troubleshooting.md) — the symptoms above, with fixes
- [Backup & upgrade](../operations/backup-and-upgrade.md) — before you move to a new
  version
