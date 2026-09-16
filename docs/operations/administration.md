# Administration & rollout

This page is for whoever owns the rollout: how to phase OpsPilot from one engineer to a
team without having to retract permissions later, what diagnostics to gather, and where
the configuration that governs a team is held. Read
[the execution boundary and risk tiers](../safety/risk-tiers.md) first — the phases below
assume you already know which human decision each tier requires.

The early phases are deliberately small. Each one widens access only after the previous
one has run against real work.

## Evaluation — week 1

One engineer, one non-production environment.

- Use a single AI provider, and prefer OpenRouter or a local Ollama model. OpenRouter needs
  one key and no procurement conversation; Ollama needs no key and no egress at all.
- Leave **Auto-run safe commands** off. It ships off; keep it that way for now.
- Enable AI on lab connections only. A connection with its AI badge off is invisible to
  every model and every assistant, which is the correct state for everything else.
- Watch every proposal, not just the ones you approve. This week is for building a feel for
  what the model proposes and how often a pattern match escalates something.

## Pilot — weeks 2 to 4

One team, with a platform engineer doing the configuration.

- The platform engineer defines the environments, the groups, and **both** profile types:
  Command Safety and Data Handling. Engineers on the team define none of these.
- Enable AI on staging connections only. Production stays dark.
- Auto-run for read-only commands is reasonable on staging in this phase, and it is where
  the value of the SRE loop becomes visible.
- Keep the dangerous-pattern list under revision. Three weeks of real use is what tells you
  which strings are missing from it.

## Production — week 5 onward

Extend to production connections, with strict profiles.

1. Start with AI off on every production connection.
2. Configure a Data Handling profile for production that also scrubs hostnames and IP
   addresses, not just credentials.
3. Configure a Command Safety profile for production with your full pattern list and
   dangerous-command confirmation on.
4. Enable AI on a handful of read-heavy production connections, auto-run off.
5. Run for two weeks. Read every proposal.
6. Widen from there.

Review the dangerous-pattern list with your security team during this phase rather than
after it, and put two points in front of them explicitly: patterns are plain,
case-insensitive substrings rather than regular expressions, and a pattern can only ever
promote a command to <span class="tier tier-high">High risk</span> — nothing in the product
demotes a command the model already flagged.

Leave auto-run off in production. Read-only auto-run is optional everywhere, and production
is where the option is not worth taking.

## Fleet

Standardise the profile set, document it internally, and treat a profile change as a
reviewed change rather than a preference.

## Configuration is per workstation

Profiles, connections, groups and environments are configured on each workstation and stay
local to it; they are not distributed from a central service. Standardising them across a
team is therefore a documented and reviewed process: write the intended profile set down,
review changes to it the way you review any other control, and check workstations against
it. Know this before promising central enforcement to a security reviewer.

On a shared workstation, restrict who can edit profiles. It is the only control point.

## Diagnostics

- **Version.** Quote the build you installed in any support conversation, taken from the
  download or installer you ran.
- **Application logs.** OpsPilot writes log output that helps support reproduce an issue.
  On Linux, the RDP launch path additionally appends to `logs/xfreerdp.log` inside the
  application data directory — see [Backup & upgrade](backup-and-upgrade.md) for how to
  locate that directory.
- **Reproduction steps.** Worth more than either of the above. See
  [Troubleshooting](troubleshooting.md) for what to gather.

## Backup

Connections, groups, environments, both profile types and your settings live in the
application data directory. Back it up before an upgrade.
[Backup & upgrade](backup-and-upgrade.md) lists what is in there, and what is not.

## See also

- [Backup & upgrade](backup-and-upgrade.md) — what to copy, and installing over an old
  build
- [Hardening checklist](../safety/hardening.md) — the settings a production phase should
  land on
- [Data Handling profiles](../safety/data-handling-profiles.md) — the redaction side of the
  profile set
- [Workflows by role](workflows-by-role.md) — what the team will actually be doing inside
  these profiles
