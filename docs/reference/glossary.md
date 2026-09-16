# Glossary

The product's terms, and the distinctions behind them. Several pairs are easy to
conflate and OpsPilot treats them as genuinely different: air-gapped against
private or VPN-only, a connection against a session, AI Providers against AI
Assistants. Where a term has a page of its own, it is linked.

Air-gapped
:   No network route to the internet at all. OpsPilot is only fully air-gapped
    when the model runs locally too — see [Offline with
    Ollama](../ai/offline-ollama.md). With a cloud provider the workstation
    still reaches the internet, which is *private or VPN-only*, not air-gapped.

AI Assistant
:   An external application you already use — Claude Desktop, ChatGPT Desktop,
    ChatGPT Web, VS Code Copilot Chat — connected to OpsPilot over
    [MCP](mcp-tools.md). It uses the subscription you already pay for rather than an
    API key. See [AI Assistants](../ai/assistants.md). Distinct from an *AI
    Provider*.

AI badge
:   The per-connection indicator showing whether the assistant may see that
    connection at all. It is not rendered for external launchers, file
    browsers, Telnet or RSH, because those sessions cannot use AI regardless.

AI Provider
:   A model backend you configure inside OpsPilot with an API key or a sign-in,
    driving OpsPilot's own AI panel. Ten ship. See [AI
    Providers](../ai/providers.md) and the [provider
    matrix](provider-matrix.md). Distinct from an *AI Assistant*.

Approval gate
:   The human decision between a proposal and execution. It is the point every
    AI path converges on, whichever model or assistant produced the proposal.
    See [Approvals & auto-run](../safety/approvals.md).

Auto-run
:   The optional setting that lets read-only commands run without an approval
    click. It ships **off**, and it is a single application-wide switch rather
    than part of a profile.

Command Safety profile
:   A named policy holding dangerous patterns and the dangerous-confirmation
    setting. Resolves connection → group → Default, and **replaces** rather
    than extends. See [Command Safety
    profiles](../safety/command-safety-profiles.md).

Connection
:   A saved definition of something to connect to — type, host, credentials,
    environment, group and AI scope. Creating one does not open anything. The
    open thing is a *session*. See [Add a
    connection](../connections/adding-connections.md).

Dangerous pattern
:   A plain-text, case-insensitive substring that forces a proposed command to
    the High risk tier. Deliberately not a regular expression. A match can only
    add the dangerous classification, never remove one the model already set.
    See [Dangerous patterns](dangerous-patterns.md).

Data Handling profile
:   A named redaction policy: which of the ten built-in categories are on, plus
    your own regular expressions. Resolves connection → group → Default. See
    [Data Handling profiles](../safety/data-handling-profiles.md).

Detached session
:   A session moved into its own window, so a terminal can sit on a second
    monitor while the AI panel stays on the first. Some main-window features do
    not follow it. See [Sessions & tabs](../workspace/sessions.md).

Environment
:   A colour-coded tag such as production or staging, used for visual
    separation and as a unit for scoping policy. `production` and `staging` are
    seeded on a new install. See [Groups &
    environments](../connections/organising.md).

Execution boundary
:   The architectural property that the AI has no execution capability — its
    only output channel is a proposal. Not a setting; the product reports it as
    **Always enforced**. See [Execution boundary & risk
    tiers](../safety/risk-tiers.md).

Group
:   A folder of connections that can carry policy. Assigning a Command Safety
    or Data Handling profile to a group applies it to everything inside, which
    makes groups the efficient place to set policy.

Hypervisor console
:   A VNC connection attached to a virtual machine's console *through its
    hypervisor* — KVM/libvirt over SSH and `virsh`, or OpenStack Nova over
    Keystone and Nova — rather than to a VNC server inside the guest. What you
    need when a machine will not boot or has no SSH. See [Hypervisor
    consoles](../connections/hypervisor-consoles.md).

MCP
:   Model Context Protocol, the standard external assistants use to reach
    tools. OpsPilot exposes itself as an MCP server bound to `127.0.0.1` only.
    See [MCP tools](mcp-tools.md).

Private / VPN-only
:   The deployment most regulated teams actually need: the targets are isolated,
    and the OpsPilot workstation reaches both them and a cloud AI. Not
    *air-gapped* — keep the two apart when describing what a deployment
    achieves.

Profile precedence
:   The order in which a profile is resolved for a session: the connection's
    own, then its group's, then Default. Because profiles replace rather than
    extend, a more specific profile can be *less* strict than Default.

Proposal
:   A command the AI suggests. It has not run and will not run until a person
    approves it. Never describe a proposal as something the AI did.

Redaction
:   The local removal of secrets from text before it reaches any AI, replacing
    each match with a typed placeholder. Every route to a model passes through
    it first. See [What gets sent](../ai/what-gets-sent.md).

Redaction badge
:   The 🔒 indicator, with a count, on any turn where something was scrubbed;
    hovering it names the categories. Hiding the badge hides the indicator
    only — redaction still happens.

Risk tier
:   One of three classifications for a proposed command:
    <span class="tier tier-readonly">Read-only</span>,
    <span class="tier tier-low">Low risk</span> or
    <span class="tier tier-high">High risk</span>. Determined by the model's own
    assessment combined with your pattern list, where the list can only make the
    tier stricter.

Secure tunnel
:   The outbound connection that lets browser and mobile ChatGPT reach OpsPilot.
    It forwards only to a validated loopback address with a token, and opens no
    public listener. See [Remote & mobile operation](../ai/remote-mobile.md).

Session
:   An open connection — a terminal, an embedded desktop or a file browser, in a
    tab. Sessions are what the AI can see, what proposals target and what
    profiles resolve against. Distinct from a *connection*.

## See also

- [How it works](../overview/how-it-works.md) — the terms above, in the order
  they occur in a real loop
- [Execution boundary & risk tiers](../safety/risk-tiers.md) — the
  distinctions that carry the most weight
- [Reference](index.md) — the rest of the lookup tables
