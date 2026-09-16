# Settings map

Where each setting lives. The settings overlay has seven pages, listed down the left
sidebar in this order.

| Page | Subtitle in the app | Groups on the page |
|---|---|---|
| General | App behaviour, fonts and layout | Appearance, Layout, Behaviour |
| Terminal | Shell appearance, scrollback and cursor | Display, Interaction, Advanced, Local Terminal |
| AI Providers | Connect Claude, ChatGPT, Gemini, Groq, Ollama and more | Provider cards, Provider Behavior |
| AI Assistants | Connect local assistants and ordinary ChatGPT chats to OpsPilot's approval-gated MCP tools | Use an AI assistant you already have, Assistant connections, Session access, Advanced |
| Environments | Add, rename, recolor or remove the environments connections can be tagged with | Environments, Add environment |
| Security | Idle timeout and session lock policy | Idle Lock, Credentials, Command Safety, Data Handling |
| About | Product, company and licensing information | Product hero, NubeStack attribution, Security guarantees |

Each page saves with its own button — **Save General Settings**, **Save Security
Settings** and so on. Nothing is saved until you press it.

## General

| Group | Control | Default |
|---|---|---|
| Appearance | **Theme** | OpsPilot Dark — the only entry |
| Appearance | **Font family** | JetBrains Mono, or Menlo on macOS |
| Layout | **Left panel default width** | 260 px (160–600) |
| Layout | **Show status bar** | On |
| Layout | **Show breadcrumb bar** | On |
| Behaviour | **Confirm before closing a session tab** | On |
| Behaviour | **Reopen last sessions on start** | Off |

One theme ships and there is no theme selector, so **Theme** has a single option.

## Terminal

| Group | Control | Default |
|---|---|---|
| Display | **Font family** | As General |
| Display | **Font size** | 13 (8–32) |
| Display | **Line height** | 1 (1.0–3.0) |
| Display | **Scrollback buffer** | 10000 lines (500–100000) |
| Display | **Cursor style** | Bar (also Block, Underline) |
| Display | **Cursor blink** | On |
| Interaction | **Copy on select** | On |
| Interaction | **Right-click pastes clipboard** | On |
| Interaction | **Bell** | None (silent), Visual flash or Sound — no option is pre-selected |
| Advanced | **Minimum terminal height** | 220 px (100–800) |
| Local Terminal | **Default shell** | Filled in per platform |

Scrollback is held in memory only. OpsPilot never writes it to disk.

## AI Providers

One card per provider, ten in all, each with its own credential fields, a **Test**
button and a model picker. Below the cards is a **Provider Behavior** group whose
settings apply only when you are using a pay-as-you-go provider — an assistant
connector is pull-based and is not affected by them.

**Auto-run safe commands** is not on the AI Providers page. It sits under
**Settings → Security →
Command Safety**, because it is a command-approval policy that applies to every
command source rather than a provider-specific behaviour. The page says so in place,
and links across.

Full detail: [AI provider matrix](provider-matrix.md) and
[AI providers](../ai/providers.md).

## AI Assistants

The MCP connector and the external apps that use it.

| Group | Control | Notes |
|---|---|---|
| Use an AI assistant you already have | **Enable connector** | Off by default. Starts the local listener |
| Assistant connections | **Claude Desktop**, **VS Code (Copilot Chat)**, ChatGPT | Each has **Connect** / **Disconnect** and a status badge |
| Assistant connections | Tunnel | **Configure** opens a panel for the tunnel ID and runtime API key |
| Session access | **Allow opening/reconnecting sessions** | Off by default |
| Advanced | **Server URL**, **Access token** | Read-only, for manual or debug connections |

**Allow opening/reconnecting sessions** is the master switch for `open_session`. It
only applies to connections that also have **Let AI Assistant open this session**
turned on, which is off by default per connection. See
[MCP tools](mcp-tools.md) and [AI Assistants](../ai/assistants.md).

## Environments

Add, rename, recolour and remove the environment tags a connection can carry. An
environment is a colour-coded tag, not a workspace. See
[Organising connections](../connections/organising.md).

## Security

The page that a security reviewer reads. Four groups.

### Idle Lock

| Control | Default |
|---|---|
| **Lock after idle** | 10 minutes (1–120) |
| **Lock on window minimize** | Off |

### Credentials

| Control | Value |
|---|---|
| **Credential storage** | **OS Keychain**, shown as a badge, not a choice |
| **Clear all saved credentials** | **Clear** button. Cannot be undone |

Where each kind of secret is held:

| Secret | Where it is stored |
|---|---|
| SSH passwords, private key passphrases | `credentials.enc.json`, encrypted by the operating system |
| OpenStack passwords and application-credential secrets | The same encrypted path |
| S3 secret access keys | The same encrypted path |
| OpenAI tunnel runtime key | Its own encrypted file, decrypted only into the tunnel process |
| AI provider API keys | `settings.json`, in plain text |

Connection credentials are fail-closed: if the operating system cannot encrypt the
credential, saving fails rather than storing it in the clear. Provider API keys take
the ordinary settings path instead — see [Security
model](../safety/security-model.md) for what that means for a review.

In 0.1.0, **Clear** removes saved AI provider configuration, including provider API
keys. It does not remove saved connection credentials; delete the connections that
hold them.

See [Credentials](../connections/credentials.md).

### Command Safety

The rows here edit the **Default** Command Safety profile — the one used by any group
or connection with nothing more specific assigned.

| Control | Default | What it does |
|---|---|---|
| **Auto-run safe commands** | Off | Read-only commands the model marks safe run with no approval click |
| **Dangerous command confirmation (Default profile)** | Toggle | On: a dangerous command needs a typed justification of at least ten characters. Off: it still needs an explicit click |
| **Command Safety profiles** | **Manage profiles…** | Edit the Default list, clone new profiles, assign per group or per connection |
| **AI direct execution** | **Always enforced** | A badge, not a toggle |

The approval buttons on a proposal card are **approve & run** and **dismiss**.

**Auto-run safe commands** applies to proposals from a configured provider and from a
connected AI Assistant alike — one policy for every command source. It is a single
application-wide switch: it is not per profile, per group or per environment.
Per-environment strictness is what Command Safety profiles are for. The page states
the reason it ships off: it trusts the model's own judgement on each command, and the
dangerous pattern list is the only thing that independently overrides it.

**AI direct execution** is reported as **Always enforced**. The model never touches a
real shell; OpsPilot's own code is what runs anything, and that boundary cannot be
changed. Turning **Auto-run safe commands** off or on changes whether a click is
needed, never who executes. Dangerous commands always require an explicit click.

See [Command Safety profiles](../safety/command-safety-profiles.md),
[Dangerous patterns](dangerous-patterns.md) and
[Execution boundary and risk tiers](../safety/risk-tiers.md).

### Data Handling

| Control | Default | What it does |
|---|---|---|
| **Redaction** | Always on | Secrets are scrubbed from terminal output before it leaves the app |
| **Show redaction badge** | Toggle | Purely a display choice |
| **Data Handling profiles** | **Manage profiles…** | Category toggles and custom patterns per profile |

Hiding the badge does not disable redaction. See
[Data Handling profiles](../safety/data-handling-profiles.md) and
[Redaction categories](redaction-categories.md).

## About

Product name and version, the NubeStack attribution and a link to the company site, a
**Security guarantees** list, and the copyright footer.

One line in the **Security guarantees** list is loosely worded: it says dangerous
commands require "a typed confirmation phrase". The real gate is a free-text
justification of at least ten characters, which doubles as a record of why the action
was approved. There is no phrase to type.

!!! note "Identifying the packaged version"
    The released version of this build is **0.1.0**. Identify what you are running from
    the download or installer you ran.

## See also

- [Execution boundary & risk tiers](../safety/risk-tiers.md) — what the Security
  page enforces
- [AI provider matrix](provider-matrix.md) — the ten provider cards in full
- [MCP tools](mcp-tools.md) — what the AI Assistants page exposes
- [Hardening](../safety/hardening.md) — a recommended starting configuration
