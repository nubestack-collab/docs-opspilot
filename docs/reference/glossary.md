# Glossary

The product's terms, and the distinctions behind them. Several pairs are easy to
conflate and OpsPilot treats them as genuinely different: air-gapped against
private or VPN-only, a connection against a session, AI Providers against AI
Assistants, a license key against a license file. Where a term has a page of its
own, it is linked.

Air-gapped
:   No network route to the internet at all. OpsPilot is fully air-gapped only
    when the model runs locally and the license needs no network either: offline
    activation or a deployment license — see [Offline with
    Ollama](../ai/offline-ollama.md). With a cloud provider the workstation
    still reaches the internet, which is *private or VPN-only*, not air-gapped.

AI Assistant
:   An external application you already use — Claude Desktop, ChatGPT Desktop,
    ChatGPT Web, VS Code Copilot Chat — connected to OpsPilot over
    [MCP](mcp-tools.md). Model usage is covered by your Claude, ChatGPT or
    Copilot plan rather than an API key; using an assistant with OpsPilot needs
    an active OpsPilot free trial or subscription. See [AI
    Assistants](../ai/assistants.md). Distinct from an *AI Provider*.

AI badge
:   The indicator next to each SSH and Local connection in the side list. It
    says whether AI is on for that connection — **AI** or **AI off** — or why
    the license keeps it off, such as **AI off · trial limit** or **AI off · no
    subscription**. A connection you turned AI off for is invisible to every
    model and every assistant. Clicking the badge turns AI on or off, except
    where it only gives a license reason. See [Groups &
    environments](../connections/organising.md).

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
:   Running a proposed command without an approval click. It is set per Command
    Safety profile with **Run commands without asking me**: **Ask me every
    time** (how every profile ships), **Only read-only commands**, or
    **Everything except dangerous ones**. A dangerous command always needs a
    click. See [Approvals & auto-run](../safety/approvals.md).

Command Safety profile
:   A named policy holding dangerous patterns and three rules: what may run
    without a click, what counts as a dangerous command, and whether a dangerous
    command needs a typed reason. Resolves connection → group → Default, and
    **replaces** rather than extends. See [Command Safety
    profiles](../safety/command-safety-profiles.md).

Connection
:   A saved definition of something to connect to — type, host, credentials,
    environment, group and AI scope. Creating one does not open anything. The
    open thing is a *session*. See [Add a
    connection](../connections/adding-connections.md).

Dangerous pattern
:   A plain-text, case-insensitive substring that forces a proposed command to
    the High risk tier. Deliberately not a regular expression. A match can only
    add the dangerous classification, never remove one. See [Dangerous
    patterns](dangerous-patterns.md).

Data Handling profile
:   A named redaction policy: which of the eleven built-in categories are on,
    plus your own regular expressions. Resolves connection → group → Default.
    See [Data Handling profiles](../safety/data-handling-profiles.md).

Deployment license
:   One organisation-wide license file, approved by NubeStack and installed by
    IT in the managed folder, for sites where nothing may leave. It is not tied
    to one computer and needs no network. See [Licensing for
    IT](../licensing/for-it.md).

Detached session
:   A session moved into its own window, so a terminal can sit on a second
    monitor while the AI panel stays on the first. It is the same session as its
    tab. Some main-window features do not follow it. See [Sessions &
    tabs](../workspace/sessions.md).

Device code
:   Six characters such as `R59-EFG` that tell computers apart. **Settings →
    License** shows it, and the NubeStack portal shows the same code next to the
    same computer, so the right one is released when several have the same
    name. It licenses nothing.

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

Free trial
:   15 days from the first launch, with no account and no email: your first 10
    saved connections, up to 10 sessions open at once, and AI on 2 connections
    at a time that you choose. Afterwards, without a subscription, the terminal
    keeps working with the same limits and AI off. See [Free trial and
    limits](../licensing/trial-and-limits.md).

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

License file
:   A `.opslic` file signed by NubeStack and imported with **Import license
    file** in **Settings → License**: an offline license for one computer, a
    renewed file, or a deployment license. Importing it needs no network.

License key
:   The `OPSP-…` key for one user's seat, used for online activation on up to 2
    devices. OpsPilot sends it once, when activating, and does not store it. IT
    can set it in `policy.json`. A key that appears in a terminal is redacted
    before anything reaches a model.

Locked connection
:   A saved connection beyond your first 10 during the free trial or without a
    subscription. It stays in the side list, greyed out with a lock, and keeps
    everything about it; it opens again after you subscribe, or when deleting
    one of the first 10 lets it in.

Managed folder
:   The machine-wide folder IT controls — `%ProgramData%\NubeStack\OpsPilot\`
    on Windows — holding `policy.json` and license files that apply to every
    user of the machine. See [Licensing for IT](../licensing/for-it.md).

MCP
:   Model Context Protocol, the standard external assistants use to reach
    tools. OpsPilot exposes itself as an MCP server bound to `127.0.0.1` only.
    See [MCP tools](mcp-tools.md).

Offline activation
:   Licensing a computer with no network: **Settings → License** shows a request
    code, which is entered in the NubeStack portal, and the license file that
    comes back is imported. Nothing on that computer touches the network. See
    [Activate OpsPilot](../licensing/activation.md).

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
:   A command the AI suggests. It has not run, and it runs only when a person
    approves it or a rule that person set in a Command Safety profile allows it.
    Never describe a proposal as something the AI did.

Redaction
:   The local removal of secrets from text before it reaches any AI, replacing
    each match with a typed placeholder. Every route to a model passes through
    it first. See [What gets sent](../ai/what-gets-sent.md).

Redaction badge
:   The 🔒 indicator, with a count, on any turn where something was scrubbed;
    hovering it names the categories. Hiding the badge hides the indicator
    only — redaction still happens.

Request code
:   The short code **Settings → License** shows under **Activate offline**.
    Entered in the NubeStack portal, it returns a license file for this
    computer. It is not secret.

Risk tier
:   One of three classifications for a proposed command:
    <span class="tier tier-readonly">Read-only</span>,
    <span class="tier tier-low">Low risk</span> or
    <span class="tier tier-high">High risk</span>. Determined by the model's own
    assessment combined with your pattern list, where, under the default **The
    AI's warning and my list**, the list can only make the tier stricter. A
    profile set to **Only my list** uses the list alone.

Secure tunnel
:   The outbound connection that lets browser and mobile ChatGPT reach OpsPilot.
    It forwards only to a validated loopback address with a token, opens no
    public listener, and needs an active trial or subscription. See [Remote &
    mobile operation](../ai/remote-mobile.md).

Session
:   An open connection — a terminal, an embedded desktop or a file browser, in a
    tab. Sessions are what the AI can see, what proposals target and what
    profiles resolve against. Distinct from a *connection*.

Session limit
:   How many sessions can be open at once: 10 in the free trial, without a
    subscription and while a paid license has a problem; no limit with a
    subscription. Tabs of every kind count, and so do programs OpsPilot starts
    in their own window. Licensing never closes an open session.

## See also

- [How it works](../overview/how-it-works.md) — the terms above, in the order
  they occur in a real loop
- [Execution boundary & risk tiers](../safety/risk-tiers.md) — the
  distinctions that carry the most weight
- [Licensing](../licensing/index.md) — the licensing terms above, in use
- [Reference](index.md) — the rest of the lookup tables
