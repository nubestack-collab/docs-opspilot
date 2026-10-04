# Safety & security

This section covers the controls that stand between an AI model and your infrastructure:
the execution boundary, the three risk tiers, the rules that decide what may run without a
click, the two kinds of policy profile, the security model a reviewer needs, and a
pre-deployment checklist.

## The safety model

- **The AI never gets a shell.** A model's output is a *proposal*. OpsPilot's own code
  types it into a session only when you approve it, or when it falls in a class you
  pre-authorised in a Command Safety profile. This is architectural, not a setting.
- **Dangerous commands always need your click.** Each profile decides what counts as
  dangerous and what else may run on its own, but no setting runs a
  <span class="tier tier-high">High risk</span> command by itself.
- **Secrets are removed locally, before egress.** Terminal output has its escape
  sequences stripped and passes through the local redactor on its way to any AI provider
  or assistant, on exactly one path that always redacts first.
- **The rules are yours, per profile.** By default your pattern list can only make
  classification stricter: a pattern match promotes a command to High risk, and the
  model's own warning still counts. A profile can be set to trust only your list.

## The pages in this section

- [Execution boundary & risk tiers](risk-tiers.md) — what the boundary is, the three
  tiers, and how the model's self-assessment and your pattern list combine. Start here.
- [Approvals & auto-run](approvals.md) — the three rules each profile sets, what each
  combination lets run without a click, the auto-run chip, what happens while a command
  waits, and the typed reason a <span class="tier tier-high">High risk</span> command can
  require.
- [Command Safety profiles](command-safety-profiles.md) — the rules and the dangerous
  pattern list, creating profiles, how they resolve, and why a profile can be *less*
  strict than Default.
- [Data Handling profiles](data-handling-profiles.md) — the eleven redaction categories,
  the six that are on by default, escape-sequence stripping, and custom regular
  expressions and how they are validated.
- [Security model](security-model.md) — the principles, the data-flow diagram, the threat
  model, licensing traffic, credential storage and session records.
- [Hardening checklist](hardening.md) — a thirteen-item checklist and a recommended
  rollout sequence, usable as a pre-deployment gate.

## Who should read what

| Reader | Read |
|---|---|
| Engineer using OpsPilot | [Risk tiers](risk-tiers.md) and [approvals](approvals.md) |
| Platform owner | Both profile pages, then [hardening](hardening.md) |
| Security or compliance reviewer | [Security model](security-model.md) and [hardening](hardening.md) |

An engineer needs the tiers and the approval flow, because that is the loop they work in.
A platform owner needs both profile pages, because profiles are the policy surface and
they are assigned per group and per connection. A reviewer needs the security model and
the hardening checklist, where the boundaries and the threat model are set out.

## See also

- [What gets sent](../ai/what-gets-sent.md) — the exact content of a request
- [Administration and rollout](../operations/administration.md) — the phased plan this
  section's policy advice fits into
- [Default dangerous patterns](../reference/dangerous-patterns.md) — the shipped list
