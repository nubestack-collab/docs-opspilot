# Execution boundary and risk tiers

This page describes the boundary the product is built on, and the three tiers a proposed
command can land in. Read it before you enable AI on a connection that matters;
everything else in this section refines what is here.

## The execution boundary

The AI model has no execution capability inside OpsPilot. It has one output channel: a
*proposal*. OpsPilot's own code is what types a command into a session, and it does so
only when you approve the proposal, or when the proposal falls in a class you
pre-authorised in the session's [Command Safety profile](command-safety-profiles.md).
Either way the command takes the same path; the model never reaches a shell.

There is no configuration, provider, assistant or prompt that changes this. It is not a
toggle, and the product does not present it as one: **Settings → Security → Command
Safety** lists **How OpsPilot runs a command** with the badge **Always enforced** rather
than a switch. With the shipped patterns, a model that decides to run `rm -rf /`
produces a card on your screen, not an outage: the command matches a dangerous pattern,
and a dangerous command always waits for your click.

!!! note "This applies to assistants too"
    A proposal that arrives from Claude Desktop, VS Code or ChatGPT over the MCP
    connector goes through the same approval gate, in the same window, with the same
    tiers and the same profile rules. There is no separate, looser path for an external
    assistant. See [AI Assistants](../ai/assistants.md).

## The three tiers

Every proposed command lands in exactly one tier, shown as a badge on its card.

| Tier | Where the tier comes from | What it takes to run |
|---|---|---|
| <span class="tier tier-readonly">Read-only</span> | The model marked the command read-only, and nothing made it dangerous | A click — or none, if the profile runs **Only read-only commands** or **Everything except dangerous ones** |
| <span class="tier tier-low">Low risk</span> | Neither read-only nor dangerous | A click — or none, if the profile runs **Everything except dangerous ones** |
| <span class="tier tier-high">High risk</span> | It counts as dangerous under the profile: the model's warning or one of your patterns | Always a click, plus a typed reason if the profile asks for one |

<span class="tier tier-readonly">Read-only</span> means viewing: reading logs, describing
or listing resources, checking status. A command that matches one of your dangerous
patterns is never read-only, even if the model tagged it so.

A <span class="tier tier-high">High risk</span> command needs a typed reason by default.
That requirement is per profile and can be turned off, in which case the command still
needs an explicit click; the click itself can never be turned off. See
[Approvals & auto-run](approvals.md).

## What makes a command dangerous

Classification comes from two independent sources:

1. **The model's own self-assessment.** The model is asked to classify each command it
   proposes as safe, dangerous, or neither.
2. **Your dangerous-pattern list**, from the Command Safety profile that applies to the
   session — plain-text, case-insensitive substrings.

How they combine is the profile's **What counts as a dangerous command** rule.

**The AI's warning and my list** (the default) uses either one: a command is
<span class="tier tier-high">High risk</span> if the model flagged it **or** it matched a
pattern. Under this rule classification moves in one direction only — a pattern match can
promote a command to High risk, and nothing demotes a command the model already flagged.

- **A model that misjudges a destructive command is still caught by your list.** This
  also covers the case where the terminal output itself is hostile: output containing
  injected instructions cannot talk a listed command down into a tier that runs by
  itself, because the pattern list is applied afterwards and independently.
- **A model that is over-cautious is never silently overridden.** If the model says
  dangerous and your list says nothing, the command is still High risk. You can approve
  it; you cannot configure the flag away under this rule.

**Only my list** makes your list the only judge. A command the model flagged but your list
does not match becomes <span class="tier tier-low">Low risk</span>, and its card carries
an amber note saying the AI flagged it as destructive and the warning was not applied.
Choose it only for a profile whose list you trust completely. See
[Command Safety profiles](command-safety-profiles.md).

When a command reaches the typed-reason box, OpsPilot says which source flagged it:
**Flagged dangerous by the AI.**, or the specific pattern it matched.

## The tiers in the AI panel

An investigation into nginx failing to start on web-01, under a profile that asks every
time:

1. The model first proposed `nginx -t`, which only tests the configuration. It was tagged
   read-only and matched no pattern, so it was
   <span class="tier tier-readonly">Read-only</span>. It ran after a click; under **Only
   read-only commands** it would have run by itself. Once finished, it folds to a single
   line.
2. The test named a misspelt directive, and the model proposed a `sed` command to correct
   the file. That changes something but is not dangerous, so it is
   <span class="tier tier-low">Low risk</span> and waits for a click (unless the profile
   runs **Everything except dangerous ones**).

    ![The finished Read-only step folded to one line, and a Low risk proposal to correct the nginx configuration with approve & run and dismiss](../assets/images/18-ai-low-risk-proposal.png)
    _The Read-only test has run and folded to one line. The correction changes a file, so
    it is **Low risk** and waits for **approve & run**._

3. Asked to clear the cache and reload nginx, the model proposed a command containing
   `rm -rf`. That matched a dangerous pattern, which made it
   <span class="tier tier-high">High risk</span> whatever the model thought. It waits
   under every setting, and with the typed reason on it names the matched pattern and
   asks for a reason.

    ![A High risk proposal to clear the nginx cache and reload, with the matched rm -rf pattern named, a typed reason and the confirm & run button](../assets/images/20-ai-high-risk.png)
    _The card says why it is **High risk**: the command matches `rm -rf` from the Default
    profile's list. **confirm & run** stays disabled until the reason is at least ten
    characters long._

## What the tiers do not cover

- They do not queue or batch. Each proposal is decided on its own.
- They do not expire on their own in the AI panel — but a proposal that arrives from a
  connected assistant does, after five minutes. See [Approvals & auto-run](approvals.md).
- They do not make a connection visible to the AI. AI access is scoped per connection by
  the **Enable AI** toggle in the connection dialog, and by your license: a connection
  with AI off is invisible to every model and every assistant, regardless of tier
  behaviour. Set the toggle deliberately and check it before you connect rather than
  assuming a default in either direction.

## See also

- [Approvals & auto-run](approvals.md) — the three rules, the defaults and the waiting
  state
- [Command Safety profiles](command-safety-profiles.md) — where your pattern list and
  rules live
- [Default dangerous patterns](../reference/dangerous-patterns.md) — the 21 shipped
  patterns
- [Security model](security-model.md) — the data flow this sits inside
