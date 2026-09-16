# Network requirements

Every network path OpsPilot uses, for a firewall or security review. Nothing is
installed on target hosts, no inbound rule to a target is needed beyond the
protocol's own listening port, and the product opens no public listener.

## Paths

| Path | Direction | Requirement |
|---|---|---|
| OpsPilot → your targets | Outbound from the workstation | Reachable over VPN or directly, on the protocol's own port |
| OpsPilot → AI provider | Outbound HTTPS | **Not needed with a local model** |
| OpsPilot → Ollama | Loopback | `http://localhost:11434`, loopback only |
| Assistants → OpsPilot | Loopback | `127.0.0.1` only, with a bearer token |
| ChatGPT Web/Work → OpsPilot | Outbound from the workstation | Outbound to the OpenAI tunnel. **No inbound listener** |

The only listener OpsPilot ever opens is the MCP connector, and it binds
`127.0.0.1` — see below. There is no inbound path to the workstation in any
configuration, including the tunnel.

## Ports to your targets

Default ports per connection type. Each is a placeholder in the dialog's port field,
so any of them can be overridden per connection.

| Connection type | Default port | Transport |
|---|---|---|
| SSH | 22 | TCP |
| Telnet | 23 | TCP |
| RSH | 514 | TCP — no port field in the dialog |
| Mosh | 22 | TCP for the SSH handshake |
| RDP | 3389 | TCP |
| VNC | 5900 | TCP |
| FTP | 21 | TCP |
| AWS S3 | none | HTTPS to the endpoint you configure |
| Serial | none | A local COM or tty device |
| Local | none | No network at all |

!!! warning "Telnet and RSH are unencrypted"
    Credentials and session content travel in clear text. Use them only on a trusted
    network segment, or for legacy gear that has no SSH.

Mosh's default port entry is 22 because that is the SSH port it uses to hand off;
OpsPilot passes it through to `mosh`'s own `--ssh` override. The UDP range Mosh then
uses is Mosh's own behaviour and is not configured by OpsPilot, so check
`mosh-server`'s documentation for your build rather than taking a range from this
page.

S3 has no default port: it goes to `https://` — the AWS regional endpoint, or the
**custom endpoint** you set for MinIO, Wasabi, Backblaze or another S3-compatible
store.

## Client prerequisites for remote desktop

These are workstation prerequisites, not target-side ones, but a desktop reviewer
needs them. Behaviour differs per platform.

| Platform | How RDP connects | What must be present |
|---|---|---|
| Windows | An in-app tab, using the bundled FreeRDP | Nothing extra — FreeRDP ships with the Windows build |
| macOS | A standard `.rdp` file handed to Microsoft's **Windows App** (`open -a "Windows App"`) | Windows App, from the Mac App Store |
| Linux | `xfreerdp` or `xfreerdp3` as an external process | FreeRDP installed from your package manager |

!!! note "FreeRDP is bundled on Windows only"
    Only the Windows build carries FreeRDP. On macOS, Microsoft's Windows App has to
    be installed; on Linux, FreeRDP comes from your package manager.

Mosh needs a `mosh` binary on the workstation — natively on macOS and Linux, or
inside WSL on Windows, which OpsPilot checks for as a fallback.

## Outbound to an AI provider

Outbound HTTPS from the workstation to the provider you configured, and nowhere
else. These are the five providers with a fixed endpoint:

| Provider | Endpoint |
|---|---|
| GitHub Copilot | `https://api.githubcopilot.com` |
| Google Gemini | `https://generativelanguage.googleapis.com/v1beta/openai` |
| Groq | `https://api.groq.com/openai/v1` |
| Mistral AI | `https://api.mistral.ai/v1` |
| OpenRouter | `https://openrouter.ai/api/v1` |

Anthropic's endpoint is not configurable from its card; OpenAI's base-URL field
placeholders `https://api.openai.com/v1`. Azure OpenAI and Custom / Self-hosted are
endpoints you supply, so the outbound destination is whatever you configure. Provider
sign-in flows also open the browser to the provider's own console — those
destinations are listed in [AI provider matrix](provider-matrix.md).

Your *targets* never need to reach a provider, and never do. Only the workstation
does.

## Outbound to Ollama, or to nothing at all

Ollama's only field is **Ollama Endpoint**, placeholdered
`http://localhost:11434`. With Ollama, or with a self-hosted OpenAI-compatible
endpoint on your own network, the workstation's outbound HTTPS requirement
disappears entirely.

!!! note "Private or VPN-only is not air-gapped"
    With a cloud provider, the workstation still reaches the internet. That is
    private or VPN-only operation. Only a local model — Ollama, or a self-hosted
    endpoint — closes the loop. See
    [Offline with Ollama](../ai/offline-ollama.md).

## Inbound: the MCP connector

| Property | Value |
|---|---|
| Bind address | `127.0.0.1` |
| Default port | 8765 |
| Path | `/mcp` |
| Authentication | A bearer token on every request, or the same token as `?token=` |
| Started | Only when you enable the connector in Settings |

Loopback only, in every configuration. Binding to loopback stops other machines, not
other local processes running as the same user, which is why the token is required as
well.

## The ChatGPT tunnel

The tunnel is an outbound process supervised by OpsPilot. It runs OpenAI's own
tunnel client and refuses any destination other than OpsPilot itself. Before the
tunnel starts, the destination has to satisfy all four of these:

- the scheme is `http`,
- the host is exactly `127.0.0.1`,
- the path is exactly `/mcp`, and
- a `token` query parameter is present.

Anything else fails rather than connecting. In addition:

- The tunnel client's own health listener is pinned to `127.0.0.1:0`, and a health
  address that is not a local HTTP address is rejected.
- The **Tunnel ID** must match the form `tunnel_` followed by at least eight
  letters, digits, hyphens or underscores.
- The runtime API key is stored through the OS credential store.

No inbound rule is created, no port is forwarded and no public address is published.
The path is outbound-only, from the workstation. See
[Remote and mobile access](../ai/remote-mobile.md).

## What is not required

- **No software on any target host.** No agent, no daemon, no sidecar, no package.
- **No inbound rule to a target** beyond the port its protocol already listens on.
- **No outbound internet access from a target.** Ever, in any configuration.
- **No account, no licence server call-home, no telemetry.** There is no cloud
  backend to reach.

## See also

- [Connection fields](connection-fields.md) — the same ports, per connection type
- [Security model](../safety/security-model.md) — the threat model behind these paths
- [Remote and mobile access](../ai/remote-mobile.md) — the tunnel in practice
- [Requirements](../getting-started/requirements.md) — workstation prerequisites
