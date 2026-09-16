# Safety & security

This section covers the controls that stand between an AI model and your infrastructure:
the execution boundary, the three risk tiers, the approval settings, the two kinds of
policy profile, the security model a reviewer needs, and a pre-deployment checklist.

## The safety model

- **The AI never gets a shell.** A model's output is a *proposal*. It becomes a real
  command only through an explicit approval event raised by your action. This is
  architectural, not a setting.
- **Secrets are removed locally, before egress.** Terminal output passes through the
  local redactor on its way to any AI provider or assistant, on exactly one path that
  always redacts first.
- **Your pattern list can only make things stricter.** A dangerous pattern can promote a
  command to <span class="tier tier-high">High risk</span>; nothing can demote one the
  model already flagged.

## The pages in this section

- [Execution boundary & risk tiers](risk-tiers.md) — what the boundary is, the three
  tiers, and the asymmetry between the model's self-assessment and your pattern list.
  Start here.
- [Approvals & auto-run](approvals.md) — the three approval settings and their defaults,
  when to enable read-only auto-run, what happens while a command waits, and the typed
  justification a <span class="tier tier-high">High risk</span> command needs.
- [Command Safety profiles](command-safety-profiles.md) — dangerous patterns and the
  confirmation requirement, how profiles resolve, and why a profile can be *less* strict
  than Default.
- [Data Handling profiles](data-handling-profiles.md) — the ten redaction categories, the
  five that are on by default, and custom regular expressions and how they are validated.
- [Security model](security-model.md) — the five principles, the data-flow diagram, the
  threat model, credential storage and session records.
- [Hardening checklist](hardening.md) — a twelve-item checklist and a recommended rollout
  sequence, usable as a pre-deployment gate.

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

- [What gets sent to a provider](../ai/what-gets-sent.md) — the exact content of a request
- [Administration and rollout](../operations/administration.md) — the phased plan this
  section's policy advice fits into
- [Default dangerous patterns](../reference/dangerous-patterns.md) — the shipped list
