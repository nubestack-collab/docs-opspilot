# Support

Support is included with an active subscription, through the channel supplied with your
subscription or distribution. This page covers the checks worth making first, what to
include in a report, and how to report a security vulnerability.

## Before you contact support

Most reports resolve against one of a handful of checks. Work through
[Troubleshooting](../operations/troubleshooting.md) first, and in particular:

- **A connection will not open.** Reproduce it outside OpsPilot — `ssh user@host` from a
  normal terminal. Confirm the VPN is up, the key format is supported, and a
  passphrase-protected key has had its passphrase entered.
- **The AI panel says no provider is active.** Open **Settings → AI Providers**,
  configure a provider and click **Test**. A provider that saves but fails the test is
  usually a wrong base URL or an expired key.
- **Ollama is not responding.** Confirm the service is running and the model is pulled
  with `ollama list`, then confirm the endpoint in OpsPilot matches, including the port.
- **An assistant does not see OpsPilot.** The connector must be enabled *and* the
  assistant connected. Restart Claude Desktop, or reload the VS Code window — both read
  MCP configuration at startup.
- **The ChatGPT tunnel will not start.** Run **Doctor** on the tunnel row. The tunnel
  forwards only to localhost, which is enforced rather than configurable.
- **Commands are not auto-running.** Auto-run ships off, so that is expected unless you
  enabled it. If it is on and a command still waits, it was not classified read-only or
  it matched a dangerous pattern — the card states which.

Check [Requirements](../getting-started/requirements.md) too if the problem appeared on
a new machine or after an operating system upgrade.

## What to include when you contact support

- **Your version.** The current release is **0.1.0**. Quote the build you
  installed — the version recorded on the download or installer you ran — rather
  than a number read off the interface.
- **Reproduction steps**, where possible — what you did, what you expected, what
  happened.
- The connection type and platform involved, since a few behaviours differ by
  platform. RDP in particular is an embedded in-app tab on Windows, and an external
  client on macOS and Linux — Microsoft's Windows App on macOS, FreeRDP on Linux.
- Whether the session had AI enabled, and which AI provider or AI Assistant was in use.

Application logs help support reproduce issues. Terminal scrollback is memory-only and
is never written to disk by OpsPilot, so if the problem is in session output you will
need to capture it yourself — and check it for secrets before you send it anywhere.

## Reporting a vulnerability

Contact NubeStack through your support channel. Security reports are handled directly by
the NubeStack engineering team, and NubeStack responds through that channel.

Do not post details of a suspected vulnerability anywhere public before NubeStack has
responded.

## See also

- [Troubleshooting](../operations/troubleshooting.md) — work through this first
- [Release notes](release-notes.md) — known limitations for the current version
- [Security model](../safety/security-model.md) — what OpsPilot does and does not
  protect against
- [Plans & subscription](plans.md) — support is included with an active subscription
