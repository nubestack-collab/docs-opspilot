# Administration & rollout

This page is for whoever owns the rollout: how to phase OpsPilot from one engineer to a
team without having to retract permissions later, how the team's workstations get
licensed, what diagnostics to gather, and where the configuration that governs a team is
held. Read [the execution boundary and risk tiers](../safety/risk-tiers.md) first — the
phases below assume you already know which human decision each tier requires.

The early phases are deliberately small. Each one widens access only after the previous
one has run against real work.

## Evaluation — week 1

One engineer, one non-production environment.

- The 15-day free trial starts when OpsPilot first launches, with no account and no email.
  It gives AI on 2 connections at a time (the engineer chooses which), up to 10 sessions
  open at once and the first 10 saved connections — enough for one engineer against a
  real workload. See [Free trial and limits](../licensing/trial-and-limits.md).
- If the evaluation needs more than the trial allows, or runs on locked-down desktops
  where OpsPilot cannot save anything in the user profile (no trial starts there), ask
  NubeStack at support@nubestack.com for an evaluation license.
- Use a single AI provider, and prefer OpenRouter or a local Ollama model. OpenRouter needs
  one key and no procurement conversation; Ollama needs no key and no egress at all.
- Leave **Run commands without asking me** at **Ask me every time** in the Default
  Command Safety profile. That is how it ships; keep it that way for now.
- Enable AI on lab connections only. A connection with its AI badge off is invisible to
  every model and every assistant, which is the correct state for everything else.
- Watch every proposal, not just the ones you approve. This week is for building a feel for
  what the model proposes and how often a pattern match escalates something.

## Pilot — weeks 2 to 4

One team, with a platform engineer doing the configuration.

- License the team before the pilot starts, so nobody runs into the trial's limits
  halfway through: one seat per user, each usable on up to 2 devices. See
  [Subscribe](../licensing/subscribe.md) for buying seats or asking for an invoice, and
  [Licensing](#licensing) below for getting them onto the workstations.
- The platform engineer defines the environments, the groups, and **both** profile types:
  Command Safety and Data Handling. Engineers on the team define none of these.
- Enable AI on staging connections only. Production stays dark.
- **Only read-only commands** is reasonable on the staging group's Command Safety profile
  in this phase, and it is where the value of the SRE loop becomes visible.
- Keep the dangerous-pattern list under revision. Three weeks of real use is what tells you
  which strings are missing from it.

## Production — week 5 onward

Extend to production connections, with strict profiles.

1. Start with AI off on every production connection.
2. Configure a Data Handling profile for production that also scrubs hostnames and IP
   addresses, not just credentials.
3. Configure a Command Safety profile for production with your full pattern list,
   **Ask me every time**, **The AI's warning and my list**, and **Make me type a reason
   for dangerous commands** on.
4. Enable AI on a handful of read-heavy production connections.
5. Run for two weeks. Read every proposal.
6. Widen from there.

Review the dangerous-pattern list with your security team during this phase rather than
after it, and put two points in front of them explicitly: patterns are plain,
case-insensitive substrings rather than regular expressions, and a pattern can only ever
promote a command to <span class="tier tier-high">High risk</span>. With **The AI's warning
and my list**, nothing in the product demotes a command the model already flagged; with
**Only my list**, the model's warning is shown on the card but not applied. Make the choice
between the two part of the review.

Keep **Ask me every time** on production profiles. Read-only auto-run is optional
everywhere, and production is where the option is not worth taking.

## Fleet

Standardise the profile set, document it internally, and treat a profile change as a
reviewed change rather than a preference.

## Licensing

OpsPilot is licensed per user. Each user's license key works on up to 2 devices, and a
license is checked on the computer itself, so it works without a network. There are three
ways to license a workstation, and they can be mixed across a team:

| Way | What happens | Network |
|---|---|---|
| Online activation | The user enters their license key in **Settings → License** | HTTPS to `license.nubestack.com`, only after activation |
| Offline activation | The computer shows a request code; it is entered in the NubeStack portal, and the license file that comes back is imported | None on that computer |
| Deployment license | One organisation-wide license file, approved by NubeStack, which IT puts in the managed folder on every machine | None |

What IT can manage centrally:

- **The managed folder** — `%ProgramData%\NubeStack\OpsPilot\` on Windows. Everything in
  it applies to every user of the machine. On Windows the OpsPilot installer creates it
  and, on every install, makes `%ProgramData%\NubeStack` writable only by administrators
  and SYSTEM, with read-only access for users.
- **`policy.json`** in that folder: `"networkActivation": "disabled"` stops all licensing
  traffic (users then activate offline or run on a deployment license); `licenseKey`
  activates online automatically; `deviceIdentity` set to `roaming` suits non-persistent
  VDI; `licenseServer` names another `https://` license server.
- **License files** in its `licenses` folder: deployment licenses and revocation lists,
  applied to every user of the machine. Deploy them with GPO, Intune, Ansible or similar.
- **Large offline fleets**: collect request codes and upload them as a CSV in the portal to
  get a zip of license files back.

OpsPilot reads the managed folder at start and every 10 minutes; **Settings → License →
Managed by your organisation → Check again** reads it at once. The exact paths on each
platform, the fields of `policy.json`, roaming profiles and VDI, and clock repair on a
deployment-license site are in [Licensing for IT](../licensing/for-it.md).

**Telling computers apart.** Each computer shows a six-character **device code**, such as
`R59-EFG`, under **Settings → License → This device**. The NubeStack portal shows the same
code next to each device, so you release the right one when several are called "OpsPilot
on Windows".

**Reimaging and replacing hardware.** A reimaged machine or new hardware is a new device:
activate it again, and release the old device in the portal if the user's two device slots
are both in use. Uninstalling OpsPilot does not free a slot: for an online activation,
choose **Deactivate this device** in **Settings → License** first; for a license file,
release the device in the portal.

## Configuration is per workstation

Profiles, connections, groups and environments are configured on each workstation and stay
local to it; they are not distributed from a central service. Standardising them across a
team is therefore a documented and reviewed process: write the intended profile set down,
review changes to it the way you review any other control, and check workstations against
it. Know this before promising central enforcement to a security reviewer.

Licensing is the exception: the managed folder above is machine-wide and can be deployed
centrally, and `policy.json` is how IT keeps licensing traffic off a network.

On a shared workstation, restrict who can edit profiles. It is the only control point.

## Diagnostics

- **Version.** **Settings → About** shows the version next to the product name, for
  example `v0.5.1`. Quote it in any support conversation.
- **License state.** **Settings → License** shows the state, the license in use and, under
  **This device**, the platform, device identity, device code and OpsPilot version. For a
  licensing question, include the headline of the state card and the device code.
- **Application logs.** OpsPilot writes log output that helps support reproduce an issue.
  On Linux, the RDP launch path additionally appends to `logs/xfreerdp.log` inside the
  application data directory — see [Backup & upgrade](backup-and-upgrade.md) for how to
  locate that directory.
- **Reproduction steps.** Often worth more than everything above. See
  [Troubleshooting](troubleshooting.md) for what to gather.

## Backup

Connections, groups, environments, both profile types, your settings and the license state
live in the application data directory; the trial and clock records also have copies under
`NubeStack` in the user profile. Back the application data directory up before an upgrade.
[Backup & upgrade](backup-and-upgrade.md) lists what is in there, and what is not.

## See also

- [Licensing for IT](../licensing/for-it.md) — the managed folder, `policy.json`,
  deployment licenses and VDI in full
- [Backup & upgrade](backup-and-upgrade.md) — what to copy, and installing over an old
  build
- [Hardening checklist](../safety/hardening.md) — the settings a production phase should
  land on
- [Workflows by role](workflows-by-role.md) — what the team will actually be doing inside
  these profiles
