# Release notes

OpsPilot **0.5.1** is the first public release of NubeStack OpsPilot. **Settings → About**
shows the version you are running; include it when you [contact support](support.md).

Installers are on <https://subscription.nubestack.com/download>. OpsPilot never updates
itself: to move to a later version, download its installer and run it. Settings,
connections and the license are kept.

## 0.5.1

The first public release.

### What is in this release

**Workspace and connections**

- Ten connection types: SSH, Telnet, RSH, Mosh, RDP, VNC (direct hosts, and KVM and
  OpenStack consoles), FTP, S3, serial and the Local Console. See
  [Connection types](../connections/connection-types.md).
- Tabs for every session, groups and environments in the side list, and a side list you
  can use from the keyboard and with a screen reader.
- A file explorer over SFTP with a built-in editor, the File Manager for transfers, FTP and
  S3 browsing.
- Remote desktop as an in-app tab on Windows, and through Microsoft's Windows App on macOS
  and FreeRDP on Linux; KVM and OpenStack consoles in an in-app tab.
- Port forwarding, command snippets, a system monitor, Insights and a set of everyday tools.
- Connection passwords and private keys encrypted with the operating system's own
  protection, and an idle lock.

**AI**

- Ten AI providers, including a local model through Ollama and any OpenAI-compatible
  endpoint you host. See [AI providers](../ai/providers.md).
- The AI panel works in SSH and Local Console tabs. It proposes one command at a time;
  nothing reaches the shell without the rules you set, and a dangerous command always
  needs your click. See [Using the AI panel](../ai/using-the-ai-panel.md).
- AI Assistants: Claude Desktop, VS Code, ChatGPT Desktop and ChatGPT Web / Work can see
  the sessions you allow and propose commands into the same approval flow, and a secure
  tunnel lets ChatGPT in a browser or on a phone reach it. See
  [AI Assistants](../ai/assistants.md).

**Safety and privacy**

- Command Safety profiles, each with three rules: **Run commands without asking me**,
  **What counts as a dangerous command** and **Make me type a reason for dangerous
  commands**. See [Approvals & auto-run](../safety/approvals.md).
- Data Handling profiles that redact secrets before anything reaches an AI provider: 11
  built-in categories, six of them on by default, including NubeStack license keys, plus
  your own patterns. Terminal control sequences are removed before redaction. See
  [Data Handling profiles](../safety/data-handling-profiles.md).
- Profiles apply per connection and per group, falling back to **Default**.
- No telemetry.

**Licensing**

- A 15-day free trial from the first launch, with no account, email or card: your first
  10 saved connections, up to 10 sessions open at once, and AI on 2 of your connections
  at a time, which you choose. See [Free trial and limits](../licensing/trial-and-limits.md).
- Without a subscription after the trial, the terminal keeps working with your first 10
  saved connections and up to 10 sessions open at once; AI, AI Assistants and the tunnel
  are off, and every setting and connection is kept.
- **Settings → License**: activate online with a license key, offline with a request code
  and a license file, or import a license file. Organisations can deploy one
  organisation-wide license and an IT policy file instead. See
  [Activate OpsPilot](../licensing/activation.md) and
  [Licensing for IT](../licensing/for-it.md).
- Online activation is optional. The trial, license files and deployment licenses make no
  network connection at all.
- Licensing never closes a session, and an AI answer already under way finishes.

### Known limitations

- The download page has the Windows x64 installer and, for Linux x64, a `.deb` package and
  an AppImage. macOS installers are not published yet; email support@nubestack.com to
  hear when they are.
- The Windows installer is signed with NubeStack's own code-signing certificate, which
  Windows does not trust by default, so it shows **Unknown publisher**. The Linux
  packages are not code-signed. Check every installer's SHA-256 against the download
  page.
- RDP certificate handling is provisional ahead of general availability; see
  [Remote desktop](../connections/remote-desktop.md).
- Telnet and RSH are unencrypted by protocol design, so credentials and session content
  travel in clear text.
- Approvals and proposals are visible in a session while it is open, and terminal
  scrollback is kept in memory only, so OpsPilot writes no durable audit log; capture
  compliance evidence from your own session-recording arrangements.
- Remote operation through the tunnel suits triage (service status, logs, confirming an
  alert); a dangerous command still needs a click in OpsPilot on the workstation.

## See also

- [Plans & subscription](plans.md): the trial, the price and what a subscription includes
- [Free trial and limits](../licensing/trial-and-limits.md): the licensing rules in detail
- [Support](support.md): what to include when you report a problem
- [Backup & upgrade](../operations/backup-and-upgrade.md): before you move to a new version
