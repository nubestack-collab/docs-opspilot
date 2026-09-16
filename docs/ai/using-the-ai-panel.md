# Using the AI panel

The AI panel sits on the right of the workspace and works on the session in the
active tab. Ask a question, read what comes back, then approve or dismiss the
command it proposes. It assumes you have configured at least one
[AI Provider](providers.md) and turned **Enable AI** on for the connection.

## Asking a question

Type into the composer at the bottom of the panel and send. The question is
answered against the recent scrollback of the session you are in, so you rarely
need to paste anything — "why did that fail?" after a failed command is usually
the whole question.

The four quick actions do the same thing with the question pre-framed:

| Quick action | Use it for |
|---|---|
| **Analyze Error** | Something just failed and you want the cause |
| **Explain Command** | You are about to run something you did not write |
| **Review Config** | A configuration file or block on screen |
| **Optimize** | Asking whether there is a better way to do this |

They appear as cards on an empty panel and in the quick-actions menu beside the
composer once a conversation has started. The same menu holds **Upload from
computer** and **Browse remote files** for attaching a file to the question —
attachments go through redaction before they are attached, and OpsPilot tells you
how many items it scrubbed.

The reply streams in as it is generated. While a turn is in flight and you have
not started typing, the composer's send button becomes a stop button, which
cancels the model's response — or, if a command's output is being awaited,
stops waiting for that. Typing wins over stopping: as soon as there is something
to send, the button goes back to Send.

## Choosing the provider for this session

The provider dropdown at the top of the panel sets which configured provider
answers *this session's* questions. It defaults to whichever provider is active
in **Settings → AI Providers**.

The setting is per session rather than global: a fast local model on the tab
where you are reading logs, a larger cloud model on the tab where you are stuck.
Switching does not restart the conversation.

## What a reply looks like

A reply is an explanation, plus — usually — **one** proposed next command rather
than a multi-step plan. OpsPilot asks the model for a single next action, runs it
if you approve, and then asks again with the real result.

Each proposed command arrives as a card with:

- the exact command text you are about to run, verbatim
- a risk badge — <span class="tier tier-readonly">Read-only</span>,
  <span class="tier tier-low">Low risk</span> or
  <span class="tier tier-high">High risk</span>
- the model's stated reason for running it

The tier is not the model's word alone. Your Command Safety profile is applied on
top of the model's own self-assessment, and a pattern match can promote a command
to High risk. Nothing can demote one that was already flagged. When the gate is
triggered by a pattern, the card names the pattern that matched, so you can tell
a model's caution apart from your own policy.

## The redaction badge

If a 🔒 badge with a count appears on a turn, redaction fired before anything was
sent. The number is how many items were replaced; hover it to see which
categories they came from.

The badge is an indicator, not a control. Turning it off in settings hides the
indicator only; redaction still happens on every turn either way.

## Approving or dismissing

The card has two buttons:

- **approve & run** — the command goes to the real shell in the real session,
  and you watch it run in the terminal.
- **dismiss** — nothing runs. The card closes and the conversation continues.

A <span class="tier tier-high">High risk</span> command adds a step: a typed
justification before the **confirm & run** button will do anything. The button
stays disabled until you have typed at least ten characters, and the reason
stays with that step in the session transcript as the record of why the
destructive action was approved. That transcript is in memory only, so capture
it yourself if you need the record kept — see
[Approvals & auto-run](../safety/approvals.md).

Once a command runs, its output is redacted and fed back as the next turn, so the
model keeps working through the problem without you re-asking after every step.
It stops when it has an answer, or when it needs a decision only you can make.

!!! note "Approval is the only path to execution"
    There is no setting, provider, prompt or assistant that lets a model run a
    command without a human approval event. Read-only commands can be
    pre-authorised as a class with **Auto-run safe commands** under
    **Settings → Security → Command Safety** — off by default — which is still a
    human authorising that class in advance rather than the model executing on
    its own.

## When a proposal came from elsewhere

If a turn originated from a connected assistant rather than the panel, the card
carries a badge naming the assistant it came from, and the panel shows which
assistant is involved. Those proposals use the same cards, the same tiers and the
same buttons — see [AI Assistants (MCP)](assistants.md).

## See also

- [AI Providers](providers.md) — configuring and testing providers
- [Execution boundary & risk tiers](../safety/risk-tiers.md) — how a tier is
  decided
- [Approvals & auto-run](../safety/approvals.md) — the approval queue and
  auto-run
- [What gets sent](what-gets-sent.md) — what leaves the machine on each turn
