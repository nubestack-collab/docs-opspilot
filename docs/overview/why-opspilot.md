# Why OpsPilot

OpsPilot brings AI assistance to infrastructure that other AI tooling cannot
reach, and it does so without giving a model access to a shell. This page covers
the capabilities that follow from those two decisions, and the limits of each.

## Disconnected and VPN-only estates

Target hosts need no internet connection. OpsPilot runs on the workstation that
already sits on both sides — reaching the isolated network over VPN, and reaching
the AI provider over the internet. The servers make no outbound call, need no
proxy exception, and require no configuration change.

```text
   Your workstation                Disconnected / VPN-only network
   ┌────────────────┐              ┌──────────────────────────────┐
   │                │   SSH/RDP    │  bastion-01   db-primary     │
   │   OpsPilot  ───┼──────────────┼─▶ web-01      core-router    │
   │       │        │   over VPN   │  (no internet, no AI SDK,    │
   └───────┼────────┘              │   no agent installed)        │
           │                       └──────────────────────────────┘
           │ redacted text only
           ▼
      AI provider  (or a local model — then nothing leaves the machine at all)
```

Nothing is installed on the target hosts: no agent, no sidecar, no daemon, no
runtime, no outbound firewall rule. Any host you can already reach is a host
OpsPilot can work against.

!!! note "Private or VPN-only is not air-gapped"
    With a cloud provider, the workstation still reaches the internet. That is
    **private or VPN-only** operation, and it is what most regulated teams need.
    A genuinely **air-gapped** deployment requires a local model, after which
    nothing leaves the machine. The interface ships every font and icon locally
    and references no external resources, so it renders at full speed on a
    workstation with no route to the internet.

## The execution boundary

The model has one output channel: a proposal. Turning a proposal into a real
command requires an approval event raised by your action. No configuration,
provider, assistant or prompt changes this, and the interface reports it as
**Always enforced** rather than offering it as a setting.

A model that proposes `rm -rf /` produces a card on screen. It does not produce
an outage.

The boundary applies equally to external assistants. A command proposed over the
MCP connector by Claude Desktop is handed to the same approval queue, and the
assistant's request waits until you act on it.

## Risk tiers

Every proposed command lands in one of three tiers.

| Tier | What it takes to run |
|---|---|
| <span class="tier tier-readonly">Read-only</span> | One click, or none if auto-run is enabled |
| <span class="tier tier-low">Low risk</span> | One explicit click, always |
| <span class="tier tier-high">High risk</span> | A typed written justification, then the click |

Classification draws on two independent sources: the model's own assessment of
the command it proposed, and your dangerous-pattern list. They combine
asymmetrically — **a pattern match can promote a command to High risk, and
nothing can demote a command the model has already flagged.** A model that
misjudges a destructive command is caught by your list; a model that is
over-cautious is never silently overridden.

Dangerous patterns are plain, case-insensitive substrings rather than regular
expressions, which makes them quick to write and quick to review. Custom
redaction patterns are the opposite case: those are real regular expressions,
and each is validated before it can be saved.

## Auto-run

Read-only work — tailing logs, describing resources, checking status — is where
an investigation spends most of its time, and approving a long series of commands
makes it easy to stop reading them.

Read-only commands can therefore run without a click. The setting ships off, and
the interface states the reason to consider before switching it on: the read-only
tag comes from the model's own assessment, and your dangerous-pattern list is the
only independent check on it. Anything that changes or deletes still stops and
waits, whichever way the setting is set.

Auto-run is a single workstation-wide switch rather than a per-profile setting.
Per-environment strictness comes from Command Safety profiles, which do resolve
per connection and per group.

## Local redaction

Terminal output is redacted on the workstation, before any of it reaches an AI
provider. Every path from a session buffer to AI context passes through the same
redaction step first.

Ten categories ship, five of them enabled by default.

| Enabled by default | Opt-in |
|---|---|
| AWS access keys | IPv4 addresses |
| Private key blocks | IPv6 addresses |
| Bearer tokens | UUIDs |
| JWTs | Email addresses |
| Password and secret assignments | Hostnames and FQDNs |

The structural categories are opt-in by design. Redacting every IP address and
hostname makes infrastructure diagnosis considerably harder, so that trade-off is
set per connection rather than applied for everyone.

You can add your own regular expressions. Each is validated with a hard timeout
before it is accepted, so a pattern that would backtrack catastrophically is
rejected at that point rather than affecting the application later.

Redaction is configured as [Data Handling
profiles](../safety/data-handling-profiles.md) attached per connection or per
group, so a production connection can scrub hostnames and addresses while a lab
connection sends everything. The same profile applies whether the destination is
a provider you configured or a connected assistant.

## Supported providers

Ten providers ship in the catalogue, and an OpenAI-compatible endpoint that is
not listed can be added as a custom provider without waiting for support.

| Provider | Notes |
|---|---|
| Anthropic Claude | API key |
| OpenAI / ChatGPT | API key, custom base URL supported |
| GitHub Copilot | Sign in with GitHub, no API key |
| Google Gemini | Sign in with Google, or an AI Studio key |
| Groq | Low-latency inference |
| Mistral AI | API key |
| **Ollama (Local)** | **Runs on your machine. Nothing leaves it.** |
| Azure OpenAI | Your own tenancy and deployment |
| OpenRouter | One key, many models, free tiers for evaluation |
| Custom / Self-hosted | Any OpenAI-compatible endpoint |

Three of these matter most for regulated buyers. **Azure OpenAI** keeps inference
inside your own cloud tenancy, under agreements your organisation has already
signed. **Custom / Self-hosted** points at any OpenAI-compatible endpoint you
run, including an internal gateway that already has approval. **Ollama** runs the
model on the workstation itself.

Inference is billed by whichever provider you choose, and retention terms are
that provider's own — check them directly for the provider you configure. A small
local model is less precise at diagnosis than a large cloud model, which is the
trade for running with no egress at all.

## Using an existing subscription

Most engineers already pay for Claude, ChatGPT or Copilot. OpsPilot can use that
subscription instead of a per-request API key by exposing itself as an MCP server
for those applications to connect to.

Claude Desktop, VS Code Copilot Chat, ChatGPT Desktop and browser-based ChatGPT
all connect. The connector binds to `127.0.0.1`, ships disabled, and requires a
token on every request, so only the applications you connect can reach it.

The safety model is unchanged when the AI is external. A command proposed by
Claude Desktop enters the same approval queue, receives the same risk tier and
meets the same justification gate as one from OpsPilot's own panel, and the
approval card names the assistant it came from.

## Remote and mobile access

The workstation stays on the VPN; you do not have to. From the ChatGPT mobile or
web app, through OpsPilot's tunnel, you can list sessions, read output and propose
commands against servers with no internet connection of their own.

```text
  Phone (ChatGPT app)  ──▶  OpenAI tunnel  ──▶  OpsPilot on your desk  ──▶  VPN  ──▶  disconnected servers
                                                        │
                                              approval still required here
```

The tunnel forwards only to OpsPilot's own loopback endpoint with a valid token;
any other destination is refused, and no public listener is opened. The runtime
API key is held with OS-level encryption and decrypted only into the tunnel
process.

This is built for remote triage — checking service status, reading logs,
confirming an alert. A High risk command requires a typed justification at the
workstation, and a proposal from an assistant expires after five minutes, so
destructive change remains a deliberate act at the machine.

## Connection types in one window

Ten connection types, all with the same AI layer, the same approval gate and the
same redaction policy:

SSH · Telnet · RSH · Mosh · RDP · VNC · FTP · AWS S3 · Serial · Local shell

A network engineer on a switch console, a Windows administrator on RDP, an SRE on
SSH and a developer pushing a build to S3 all work in the same window with the
same controls around them.

!!! warning "Telnet and RSH are unencrypted"
    Credentials and session content travel in clear text. Both are supported
    because switch and appliance consoles still require them; use them on trusted
    management networks.

RDP is an embedded tab on Windows and an external client window on macOS and
Linux, because the window-embedding interface the Windows behaviour depends on
has no counterpart on those platforms. Every other connection type behaves
identically across all three.

## Deploying and verifying a change

Write the code in your usual authoring tool. Deploy it through OpsPilot, verify
it through OpsPilot, and read the failing logs through OpsPilot — then take the
diagnosis, approve the fix and watch it apply.

OpsPilot does not replace an authoring tool. It covers the environment the code
has to run in, which is frequently one the authoring tool has no access to.

## See also

- [How it works](how-it-works.md) — these capabilities as a single loop, step by
  step
- [Execution boundary & risk tiers](../safety/risk-tiers.md) — the boundary and
  the tiers in detail
- [Offline with Ollama](../ai/offline-ollama.md) — running with no egress
- [Remote & mobile operation](../ai/remote-mobile.md) — the tunnel and its scope
