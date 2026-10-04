# First run

OpsPilot opens with nothing configured, nothing connected and nothing sent anywhere,
and with its 15-day free trial running. This page describes what is in front of you
after the first launch, and what to do with it.

![OpsPilot during the free trial with no sessions open: the Trial · 15 days left chip in the title bar, saved connections in the side list with their AI badges, and the AI panel in its ready state](../assets/images/14-trial-first-launch.png)
_The window during the free trial, here after a few connections have been saved; on a
new install the list holds only **Local**. The title bar shows **Trial · 15 days
left**. In the side list, web-01 and web-02 hold the trial's two AI places and show
**AI**; bastion-01 and staging-app have AI turned on but show **AI off · trial
limit**; **Local** and db-01 show **AI off**. The two connections in the Network group
are Telnet and RDP, which have no AI badge. The AI panel says **ready**, which means the
panel is loaded, not that a model is configured._

## What you are looking at

- **The trial chip.** The title bar shows **Trial · 15 days left**, counting down each
  day to **Trial · ends today**. Choose it to open **Settings → License**. In the last
  three days a notice below the tabs also offers **Subscribe** and **Dismiss**.
- **An empty connections list.** No sample hosts, no imported profiles, no discovery
  scan.
- **A `Local` entry.** A shell on the machine you are sitting at, available without
  configuring anything. Use it to check that the terminal works, and for the
  local-side half of a task. It ships with AI off, so its badge reads **AI off**.
- **The AI panel, in its ready state.** It offers **Analyze Error**, **Explain
  Command**, **Review Config** and **Optimize** as starting points, and the composer
  shows an **Auto-run off** chip. No provider is selected because none is configured.
- **No open sessions.** The session tab bar is empty until you connect to something.

Nothing has been sent anywhere, because there is nowhere to send it to and no session
to read from. There is also no account behind any of this: the trial started without
one, and settings, connections and profiles are local files on this workstation.

## The AI badges in the side list

Every saved connection that can use AI — SSH connections and **Local** — carries a
badge that says what applies to it, whether it is open or not:

- **AI** — AI is on for this connection.
- **AI off** — AI is turned off for it.
- **AI off · trial limit** — during the trial, this connection does not hold one of
  the 2 places where AI can be on. Click the badge to give it one.
- Other **AI off** and **AI paused** badges say why the license keeps AI off, for
  example **AI off · no subscription** once the trial has ended. Point at a badge for
  the full reason.

During the trial, AI can be on for 2 of your connections at a time, and you choose
which. On a new install, the first two connections you save with **Enable AI** on take
those two places; to move AI to another connection, click its **AI off · trial limit**
badge and choose **Use AI on … instead of …**. The trial also allows up to 10 sessions
open at once and your first 10 saved connections. See
[Free trial and limits](../licensing/trial-and-limits.md).

## What to do first

1. **Open a `Local` session.** Double-click **Local**. You get a real shell in a real
   terminal, which confirms the install is sound.
2. **Create your first real connection.** Use **+ new connection** at the foot of the
   list. See [Add a connection](../connections/adding-connections.md), or follow
   [Quickstart](quickstart.md) if you want the whole loop in one pass.
3. **Decide how the AI reaches you** — a provider you supply credentials for, a local
   model, or an external assistant you already pay for. All three are covered in
   [AI Providers](../ai/providers.md) and [AI Assistants](../ai/assistants.md).
4. **Set your safety policy** before you give the AI anything interesting to look at.
   In **Settings → Security → Command Safety** you set, for the Default profile, which
   commands may run without asking you and what counts as a dangerous command.

## AI access is scoped per connection

Configuring a provider does not make the AI able to see your sessions. Each connection
carries its own **Enable AI** toggle, and a connection with it off is invisible to
every model and every assistant — no scrollback, no host details, nothing.

!!! warning "Set the toggle deliberately on each connection you care about"
    Check **Enable AI** in the connection dialog every time you create or edit a
    connection, rather than relying on what the dialog shows when it opens. The
    seeded **Local** connection ships with AI off.

Rollout can therefore be incremental and reversible, provided you set the toggle
actively. Turn AI off everywhere you are not ready to expose, enable it on a
development host, watch how the loop behaves, and only then widen it. A connection you
have confirmed as off never participates, whatever else you configure.

!!! note "Before any AI is configured"
    OpsPilot is a complete terminal, file manager and remote-desktop client on its
    own. If you never configure a provider, that is what you have, and it sends
    nothing anywhere.

## See also

- [Quickstart](quickstart.md) — first connection to first approved command in about
  five minutes
- [Add a connection](../connections/adding-connections.md) — every field in the
  connection dialog
- [Free trial and limits](../licensing/trial-and-limits.md) — the trial's AI places,
  session limit and saved connections
- [Approvals & auto-run](../safety/approvals.md) — what happens when the AI proposes
  something
