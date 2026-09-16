# Hardening checklist

Work through the checklist before OpsPilot goes anywhere near a production estate, then
follow the rollout sequence rather than enabling everything at once. The review table at
the end is what keeps both true six months later.

## Pre-deployment checklist

- [ ] Set an idle lock timeout and enable lock on minimise
      (**Settings → Security**).
- [ ] Enable AI only on the connections that need it. **Enable AI** is per connection, so
      open each production connection and confirm the toggle rather than assuming it is
      off — the new-connection dialog does not open in the off state.
- [ ] Confirm that no Telnet or RSH connection is being counted as covered by a profile.
      Those sessions have no AI path, so a profile assigned to them does nothing.
- [ ] Assign a strict Data Handling profile to production groups.
- [ ] Assign a strict Command Safety profile to production groups, with a full pattern
      list.
- [ ] Leave auto-run off in production.
- [ ] Keep dangerous-command confirmation on everywhere.
- [ ] Leave assistant session-opening off unless it is required — **Let AI Assistant open
      this session** is per connection and off by default, and the master switch in
      **Settings → AI Assistants** gates it as well.
- [ ] Stop the ChatGPT tunnel when it is not in use.
- [ ] Use a local model where the data classification requires it. A cloud provider means
      the workstation still reaches the internet.
- [ ] Verify installer hashes before deployment.
- [ ] Restrict who can edit profiles on shared workstations.

Supporting facts for whoever signs this off:

- **Credential storage is fail-closed.** Credentials are encrypted through the OS
  credential store, and a workstation that cannot encrypt refuses to save the credential
  rather than writing plaintext. That includes an S3 secret access key, which occupies the
  same encrypted slot as an SSH password.
- **A connection with AI off is invisible to every model and every assistant**, which
  makes the second item on the list the cheapest control on this page.
- **Run OpsPilot over a VPN or a trusted management network.** On the SSH path the
  network position is what protects the connection. On Linux, an RDP password is passed to
  the FreeRDP client on its command line and is therefore readable by anything that can
  list the user's processes, so a shared Linux workstation is unsuitable for saved RDP
  credentials. See [Security model](security-model.md).
- **Session transcripts are held in memory for the life of the session** and are not
  written to disk. Where a durable execution record is required, capture it from your own
  session-recording arrangements.

## Recommended rollout policy

Do these in order. Each step is reversible, and each one teaches you something before the
next widens the exposure.

1. **Turn AI off on every production connection.** Do this actively, connection by
   connection: open each one and set **Enable AI** off. Do not treat it as inherited —
   the new-connection dialog does not open in the off state, so an imported or
   recently-created connection may well have it on.
2. **Configure a production Data Handling profile** that also scrubs hostnames and IP
   addresses, on top of the five credential categories that are on by default. Expect
   answers to get vaguer; decide whether that trade is acceptable before, not after.
3. **Configure a production Command Safety profile** with your full pattern list and
   dangerous-command confirmation on. Remember that a profile replaces Default rather
   than extending it, so the production profile must be complete in itself.
4. **Enable AI on a handful of read-heavy production connections**, with auto-run off.
   Read-heavy means the sessions where the work is inspection: log hosts, monitoring
   boxes, read replicas.
5. **Run for two weeks and read every proposal.** Not a sample — every one. You are
   calibrating two things at once: whether the model's risk self-assessment matches your
   estate, and whether your pattern list catches what it misses.
6. **Widen from there**, one group at a time, with the same two-week discipline on any
   environment class you have not seen before.

!!! warning "Do not start step 4 with a write-heavy connection"
    The first connections you enable are the ones whose proposals you will read most
    carefully, which is exactly why they should be the ones where a mistake costs least.
    A database primary is a bad first choice even with auto-run off.

## What to review, and when

| Review | When | What you are looking for |
|---|---|---|
| Profile assignments per group and per connection | At rollout, then whenever a connection moves group | A permissive profile that has landed on a production group — because profiles replace rather than extend, this silently drops every Default pattern |
| The dangerous-pattern list | With the security team, and after any new tooling lands | Estate-specific destructive verbs no default could guess |
| Which connections have **Enable AI** on | Periodically, and after any connection import | Scope creep — connections that were enabled for one investigation and never turned back off |
| Which connections allow assistant session-opening | Same review | The stricter permission, which should be a much shorter list |
| Custom redaction patterns | When a new data class appears in output | Patterns that no longer match a changed identifier format, and new classes with no pattern at all |
| Idle lock and lock-on-minimise settings | After any settings reset or reinstall | Settings are per workstation, so a rebuilt machine starts from defaults |

These reviews are the standing record of policy on the workstation, so put them on a
schedule and keep the outcome with your own change records.

## Who should own the profiles

Both profile types should be owned by the platform team, not by the individual engineer
using the connection:

- A profile is policy for everyone assigned to a group, so editing one on a whim changes
  the rules for colleagues who were not part of the decision.
- The judgment involved — which commands are destructive in *this* estate, which data
  classes must never reach a provider — is estate knowledge, not session knowledge.

A practical arrangement: a platform engineer defines the environments, the groups and
both profile types; profile changes are treated as a reviewed change with the security
team; individual engineers assign connections to groups and otherwise leave profiles
alone. On shared workstations, restrict who can edit them at all.

## See also

- [Security model](security-model.md) — the reasoning behind each item
- [Command Safety profiles](command-safety-profiles.md) — building the production pattern
  list
- [Data Handling profiles](data-handling-profiles.md) — building the production redaction
  profile
- [Administration and rollout](../operations/administration.md) — the phased plan by week
