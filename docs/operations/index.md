# Operate

This section covers running OpsPilot as part of a working day, and rolling it out to a
team. It assumes you have OpsPilot installed, at least one connection saved, and either an
AI provider configured or an assistant connected.

Nothing here changes the execution boundary. Every workflow and every rollout phase below
still ends with a person deciding about a command — see [the execution boundary and risk
tiers](../safety/risk-tiers.md).

## Pages in this section

- **[Workflows by role](workflows-by-role.md)** — five loops: developer, SRE, network
  engineer, DevOps and platform team. Read the one that matches your job, then the platform
  team section, which covers the guardrails the other four work inside.
- **[Administration & rollout](administration.md)** — a four-phase rollout from one
  engineer on a lab host to a standardised profile set, how a team's workstations get
  licensed, and where the configuration that governs a team is held.
- **[Backup & upgrade](backup-and-upgrade.md)** — which files hold your connections,
  profiles, settings and license state, what a copy of them does not include, how to
  install a new build over an old one, and how to import a renewed license file.
- **[Troubleshooting](troubleshooting.md)** — symptoms with what to check for each: SSH,
  providers, assistants, the tunnel, auto-run, redaction and RDP, then the licensing
  messages (session limit, locked connections, AI off or paused, released computers).

For deploying licenses across a fleet — the managed folder, `policy.json`, deployment
licenses and VDI — see [Licensing for IT](../licensing/for-it.md).

## See also

- [Hardening checklist](../safety/hardening.md) — the settings a production rollout should
  land on
- [Approvals & auto-run](../safety/approvals.md) — which human decision each tier requires
- [Licensing](../licensing/index.md) — the trial, subscribing and activation
- [Support](../about/support.md) — what to include when you raise an issue
