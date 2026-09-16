# Approvals and auto-run

Three settings govern what a proposal has to pass through before it runs. Two are yours
to change; the third is not a setting at all. They live under **Settings → Security →
Command Safety**, alongside the profile management buttons.

## The three settings

| Setting | Default | Effect | Scope |
|---|---|---|---|
| **Auto-run safe commands** | **Off** | Commands the model tagged read-only run with no click | Application-wide |
| **Dangerous command confirmation** | **On** | A High risk command needs a typed reason, not just a click | Per Command Safety profile |
| **AI direct execution** | **Always enforced** | Not configurable — the AI never touches a shell | Architectural |

The scope column matters when you write policy. **Dangerous command confirmation** is a
property of the Command Safety profile that resolves for a session, so it can differ
between production and a lab. **Auto-run safe commands** is a single application-wide
switch on the workstation, so it is on or off for every session you have open.

## When to enable auto-run

Read-only work — tailing logs, describing resources, checking status — is where an
investigation spends most of its time, and approving a long series of commands makes it
easy to stop reading them. Read-only commands can therefore run without a click.

The setting ships off, and the interface states the reason to weigh before switching it
on: the read-only tag is the model's own judgment, and your dangerous-pattern list is the
only independent check on it.

Switch it on where the blast radius is one you accept. Anything that changes or deletes
still stops and waits, whatever the setting says — auto-run applies only to the
<span class="tier tier-readonly">Read-only</span> tier, and a pattern match removes a
command from that tier before the auto-run check ever sees it.

The AI panel carries an **Auto-run on** / **Auto-run off** chip, so the current state is
visible where the proposals appear rather than only in settings.

## Guidance by environment

| Environment | Auto-run | Confirmation | Patterns |
|---|---|---|---|
| Production | Off | On | Full list |
| Staging | On | On | Full list |
| Lab / sandbox | On | Optional | Minimal |

Read this as policy for the estate rather than as three switches. The pattern list and
the confirmation requirement are per profile, so they genuinely differ per environment
once you have assigned profiles to groups. Auto-run does not: it is one switch for the
workstation. If you work in production from the same machine as your lab, that means
leaving auto-run off.

## While a command is waiting

OpsPilot makes a pending approval visible outside its own window:

- **A count on the application icon.** On Windows this is a taskbar overlay icon showing
  the number of pending approvals; on macOS it is the dock badge. Electron exposes
  neither on Linux, so there is no icon badge there.
- **A desktop notification** naming the session and the tier, with the start of the
  command text.
- **Clicking the notification brings OpsPilot to the foreground**, switches to the right
  session and scrolls the pending proposal into view. The window restores itself if it
  was minimised.

Inside the window, a pending row renders expanded, showing the full command text and two
buttons — **approve & run** and **dismiss** — and collapses to a single line once it has
been resolved.

## High risk: the typed justification

When **Dangerous command confirmation** is on for the resolved profile, a
<span class="tier tier-high">High risk</span> command does not offer an approve button.
It offers a reason box, and the **confirm & run** button stays disabled until you have
typed at least ten characters. The button then has to be clicked (or Enter pressed); it
does not fire the moment you cross the threshold, so it will not run mid-sentence.

The gate also names why the command was flagged: the matched pattern and its profile, or
that the AI flagged it itself. The justification you type is the confirmation — there is
no separate "type RUN to continue" step — and it stays with that step in the session
transcript.

!!! note "The justification stays with the session"
    The session transcript, including the justification you type, is held in memory for
    the life of the session. If you need a durable record of what was run and why,
    capture it from your own session-recording arrangements. See
    [Session records](security-model.md).

Turning the setting off does **not** remove the approval. The card falls back to the same
**approve & run** / **dismiss** row a Low risk command gets: an explicit click is still
required, and only the written reason goes away. Leave it on everywhere unless you have a
concrete reason not to.

## Proposals from a connected assistant

When the proposal came from Claude Desktop, VS Code or ChatGPT rather than from
OpsPilot's own AI panel, one thing differs: it expires. The MCP connector waits five
minutes for a human decision, then reports back that there was no response. The row in
the panel shows as *proposal expired* and is no longer waiting on anything; the assistant
will usually re-propose it as a fresh turn.

Destructive change from a phone is therefore deliberately impractical. A High risk
command needs a typed justification at the workstation, and a proposal does not sit
waiting for an hour while you walk back to your desk. See
[Remote and mobile operation](../ai/remote-mobile.md).

## See also

- [Execution boundary & risk tiers](risk-tiers.md) — where the tiers come from
- [Command Safety profiles](command-safety-profiles.md) — the pattern list and the
  per-profile confirmation setting
- [Settings map](../reference/settings-map.md) — what lives on which settings page
- [Hardening checklist](hardening.md) — the rollout sequence these defaults belong to
