# Operate

This section covers running OpsPilot as part of a working day, and rolling it out to a
team. It assumes you have OpsPilot installed, at least one connection saved, and either an
AI provider configured or an assistant connected.

Nothing here changes the execution boundary. Every workflow and every rollout phase below
still ends with a person approving a command — see [the execution boundary and risk
tiers](../safety/risk-tiers.md).

## Pages in this section

- **[Workflows by role](workflows-by-role.md)** — five loops: developer, SRE, network
  engineer, DevOps and platform team. Read the one that matches your job, then the platform
  team section, which covers the guardrails the other four work inside.
- **[Administration & rollout](administration.md)** — a four-phase rollout from one
  engineer on a lab host to a standardised profile set, what to enable in which phase, and
  where that configuration is held.
- **[Backup & upgrade](backup-and-upgrade.md)** — which files hold your connections,
  groups, environments and profiles, what a copy of them does not include, and how to
  install a new build over an old one on each platform.
- **[Troubleshooting](troubleshooting.md)** — eight symptoms, what to check in order for
  each, and which behaviours are enforced by design.

## See also

- [Hardening checklist](../safety/hardening.md) — the settings a production rollout should
  land on
- [Approvals & auto-run](../safety/approvals.md) — which human decision each tier requires
- [Support](../about/support.md) — what to include when you raise an issue
