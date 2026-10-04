# What OpsPilot is

OpsPilot is a desktop operations workbench. It is a terminal, a file manager, a
remote-desktop client and an AI assistant in one application, and it is built
around a single idea:

!!! quote ""
    **The AI helps you operate your infrastructure. It never operates your
    infrastructure itself.**

## What you do with it

You connect to your servers, switches, storage and desktops the way you already
do — SSH, Telnet, RSH, Mosh, RDP, VNC, FTP, AWS S3, serial or a local shell. In
SSH and local shell sessions, OpsPilot follows the session alongside you. When
you ask it a question, it reads the scrollback, removes the secrets locally,
sends only the redacted text to the model you chose, and returns an explanation
together with a proposed command.

That command does not run by itself. It appears in front of you, labelled with a
risk tier and showing the exact text you are about to execute. You decide, or you
decide in advance: a Command Safety profile can let read-only commands run
without a click, and a dangerous command always waits for one.

![The OpsPilot window with grouped saved connections and their AI badges on the left, an SSH session in the centre, and the AI panel on the right showing three finished steps and a summary of the fix](../assets/images/19-ai-summary.png)
_One window covers the whole job. On the left are saved connections, in groups and
colour-tagged by environment, each with a badge that says whether AI is on for it:
**AI**, **AI off**, or, during the free trial, **AI off · trial limit**. In the centre
is the live session; on the right, the AI panel after a fix, with each command it
proposed listed under its risk tier and a summary at the end. A connection without AI
on is invisible to every model and every assistant._

## How it differs from an AI coding tool

An AI coding or ops tool normally assumes two things: that the machine being
worked on can reach the internet, and that the model should be given a shell.
OpsPilot inverts both. Your servers can stay disconnected, because only your
workstation needs to reach the model — and with a local model, not even that. The
model is never given a shell: it can propose, and it cannot execute. Those two
inversions are what let AI into regulated banking, defence, healthcare,
industrial control and classified networks.

AI access is set per connection, by the **Enable AI** toggle in the connection
dialog. Set it deliberately and check it before you connect: a connection with AI
off is invisible to every model and every assistant.

## What it does not do

OpsPilot installs nothing on the target hosts. There is no agent, no sidecar, no
daemon, no runtime to deploy and no outbound firewall rule to request.

It sends no telemetry, and there is no account to sign in to inside the
application. Connections, groups, environments and profiles live in the
application's own data directory on your workstation. Terminal scrollback is held
in memory, and OpsPilot writes it to disk only through the explicit **Save
terminal output** and **Print terminal output** actions.

The only service OpsPilot contacts on its own is NubeStack's license server, and
only if you choose to activate a license online. The free trial, offline license
files and organisation-wide deployment licenses make no licensing connection at
all. Licenses are signed by NubeStack and checked on your workstation. See
[Licensing](../licensing/index.md).

!!! note "Private or VPN-only is not the same as air-gapped"
    With a cloud provider your *workstation* still reaches the internet, which is
    private or VPN-only operation. A genuinely air-gapped deployment needs a
    local or self-hosted model and an offline license file or a deployment
    license, after which nothing leaves the machine.

## See also

- [Why OpsPilot](why-opspilot.md) — the capabilities, and the mechanism behind
  each one
- [How it works](how-it-works.md) — the eight steps from question to executed
  command
- [Execution boundary & risk tiers](../safety/risk-tiers.md) — how a proposal
  becomes a command
- [Quickstart](../getting-started/quickstart.md) — install and try the loop
  during the free trial
