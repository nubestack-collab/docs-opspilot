# Security model

This page is written for the reviewer who has to sign OpsPilot off. It states the
principles the product is built on, the path data takes, what it stores and where, and
what it keeps of a session.

## Principles

1. **The AI proposes, the human disposes.** The model has one output channel: a proposal.
   OpsPilot's own code is the only thing that executes against a session, and it does so
   only in response to an approval event raised by a user action. This is architectural.
   There is no setting, provider, assistant, prompt or command-line flag that changes it,
   and the product reports it as **Always enforced** rather than as a toggle.
2. **Redact before egress.** Terminal content reaches no AI provider or assistant without
   passing through the local redactor first, on one entry point shared by every path.
   Content with no connection context resolves to the Default profile rather than
   bypassing redaction.
3. **Least exposure.** AI access is scoped per connection by the **Enable AI** toggle, and
   the off state is meaningful: a connection with it off is invisible to every model and
   every assistant. Set it deliberately per connection rather than assuming a default —
   the seeded **Local** connection ships with AI off, but the new-connection dialog does
   not open in that state, so verify the toggle before connecting. Letting an external
   assistant *open* a session is a separate and stricter permission again — **Let AI
   Assistant open this session**, off by default and additionally gated by a master switch
   in **Settings → AI Assistants**.
4. **Local by default.** No telemetry, no account system, no cloud backend in the core
   product. Settings, connections, groups, environments and both kinds of profile are
   files on the workstation. Terminal scrollback is held in memory, and OpsPilot never
   persists it of its own accord — the only exceptions are the explicit, user-initiated
   **Save terminal output** and **Print terminal output** actions on a session's tab
   menu.
5. **Nothing on the target host.** OpsPilot installs no agent, daemon, sidecar or runtime
   on the hosts it works against, and needs no inbound route or outbound firewall rule
   for them. It operates over the access you already have.

## Data flow

```text
terminal buffer
     │
     ▼
 redactor  ◀── Data Handling profile (connection → group → Default)
     │
     ▼
 AI provider  or  connected assistant
     │
     ▼
 proposal + risk self-assessment
     │
     ▼
 Command Safety profile  ── patterns may escalate, never de-escalate
     │
     ▼
 tier: read-only │ low risk │ high risk
     │              │            │
  auto-run?       click      typed reason + click
     │              │            │
     └──────────────┴────────────┘
                    ▼
              real shell
                    │
                    ▼
           output → redactor → next turn
```

The loop at the bottom is easy to miss on a first reading: the output of an approved
command is redacted again before it becomes context for the following turn.

## What never leaves the machine

Credentials, private keys, the connection store, the contents of AI-disabled sessions,
and — with a local model — everything. The target hosts never need internet access; only
the workstation does, and only when the configured provider is a remote one.

!!! note "Private or VPN-only is not air-gapped"
    With a cloud provider, the workstation still reaches the internet. That is **private
    or VPN-only** operation. A genuinely air-gapped deployment requires a local model —
    see [Running offline with Ollama](../ai/offline-ollama.md).

## Threat model

The threat model does not assume the AI provider is malicious. It assumes terminal output
is sensitive by default and that engineers are fallible by default, and builds controls
for both. The risks it covers for a terminal-session-to-AI pipeline:

| Risk | Control in the shipped product |
|---|---|
| **Credentials in output** — passwords echoed to screen, keys in an `env` dump, tokens in a `cat` of a config file, private key contents | The five credential redaction categories, on by default |
| **Customer-identifying data** — internal hostnames, IP ranges, tenant or project names that reveal who a client is or how their estate is laid out | The five structural categories, opt-in per profile, plus custom patterns |
| **Regulated data** — anything carrying a data-residency or sector obligation | Custom patterns, a production Data Handling profile, or a local model so nothing leaves the machine |
| **Destructive commands** — a suggestion that is technically correct and catastrophic if approved without being read | The approval gate, the three tiers, the dangerous-pattern list and the typed justification |
| **Credential-at-rest risk** — credentials stored carelessly by the tool itself | OS-native credential storage, described below |
| **MITM on SSH** — silently trusting an unknown host key | The network path: run OpsPilot over a VPN or a trusted management network |
| **Third-party data handling** — what a provider does with what is sent, and for how long | Redaction first; provider choice, including a local model, second |

!!! warning "Telnet and RSH are unencrypted"
    Credentials and session content travel in clear text. Both are supported because
    switch and appliance consoles still require them; use them only on trusted
    management networks. See
    [Connection types](../connections/connection-types.md).

## The five-stage pipeline

| Stage | What happens | Key control |
|---|---|---|
| 1. Terminal session | Raw output is captured in the application | Nothing is sent anywhere yet, and it stays in memory |
| 2. Local redaction | Output is scrubbed **before** it is added to AI context | Runs entirely on-device, no network call |
| 3. Provider or assistant | Redacted context is sent for diagnosis and a suggested command | Provider is yours to choose, including a local one |
| 4. Approval gate | The suggestion is shown as a card and is never typed into the shell for you | An explicit click; a typed reason for High risk |
| 5. Execution | The command runs in the real session, and its output is redacted again on the way back | The same single redaction entry point |

## Session records

Session transcripts and terminal scrollback are held in memory for the life of the
session. OpsPilot does not write them to disk, and the two exports available are
**Save terminal output** and **Print terminal output** on a session's tab menu, which
capture the scrollback text. If your estate needs a durable execution record, capture it
from the host side or from your own session-recording arrangements.

## Credential storage

SSH passwords, private keys and passphrases are encrypted through the operating system's
own credential store — Keychain on macOS, Credential Manager on Windows — and never
written to a plaintext configuration file. The connection dialog says so directly on its
**Save connection** toggle: *credentials encrypted, never plaintext*.

Details to record in a review:

- **There is no plaintext fallback.** If OS encryption is unavailable, storing the
  credential fails with an error rather than silently downgrading.
- **The OpenAI tunnel's runtime key is held separately**, encrypted the same way and
  decrypted only into the tunnel process. The tunnel additionally refuses to start when
  the platform's only available encryption backend is the plaintext one.

!!! note "AI provider API keys are held differently from connection credentials"
    Provider API keys are stored in the application's own settings file on the
    workstation, not in the OS credential store that holds connection credentials. Treat
    a provider key as you would any other credential in a configuration file: scope what
    it can reach, rotate it on the provider's side when needed, and take particular care
    on a shared or multi-user workstation.

    The **Clear** button in **Settings → Security** removes saved AI provider
    configuration, including those keys. Connection credentials are cleared by deleting
    the connections that hold them.

    If your review needs inference credentials held to the same standard as SSH
    credentials, the options are a local model, which needs no key at all, or a
    self-hosted endpoint on your own network. Both are covered in
    [Offline with Ollama](../ai/offline-ollama.md) and
    [Self-hosted & enterprise](../ai/self-hosted.md).

!!! warning "RDP passwords on Linux are visible to local process listing"
    On Linux, an RDP password is passed to the FreeRDP client on its command line. It is
    never written to disk, but anything that can list the user's processes on that
    workstation can read it. Treat a shared or multi-user Linux workstation as unsuitable
    for saved RDP credentials.

## Third-party provider handling

If you use a remote provider, its own terms govern what happens to the redacted text you
send, and those terms — retention windows, whether inputs are used for training, and the
contractual mechanisms available to change either — are the provider's own and change
over time. Check the current terms for the provider you configure, and treat them as the
second line of defence rather than the first: local redaction is what runs before
anything is sent.

## Reporting a vulnerability

Contact NubeStack through your support channel. Security reports are handled directly by
the NubeStack engineering team.

## See also

- [Hardening checklist](hardening.md) — the same material as a pre-deployment gate
- [Data Handling profiles](data-handling-profiles.md) — the redaction layer in detail
- [Execution boundary & risk tiers](risk-tiers.md) — principle 1, in practice
- [Credentials](../connections/credentials.md) — where credentials are entered and stored
