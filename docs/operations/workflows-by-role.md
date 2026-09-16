# Workflows by role

Five loops, one per kind of engineer who uses OpsPilot. The mechanic is the same in each —
work normally, ask a question, read a proposal, decide — but the decision you make and the
tier it arrives at differ by role. Read the section that matches your job, then the
platform team section at the end, which covers who sets the guardrails the other four work
inside.

## Developer — code, deploy, verify

You have written the change somewhere else. This loop is about getting it onto a host and
confirming it came up.

1. Write the code in your usual place — Codex, Claude Code, or your IDE. OpsPilot is not
   an editor for this step.
2. In OpsPilot, open a session on the target environment. If the environment tag is
   **Staging** or **Lab**, the group's Command Safety profile is already resolving behind
   the session; you do not set anything per session.
3. Pull and build: `git pull && npm ci && npm run build`. That lands as
   <span class="tier tier-low">Low risk</span> — one click to approve, no typed reason.
4. Deploy: `systemctl restart app`. Also <span class="tier tier-low">Low risk</span>, one
   click. If your production Command Safety profile lists `systemctl stop` as a dangerous
   pattern, note that a restart and a stop are different strings — decide deliberately
   which one you want in the list.
5. Verify by asking, rather than by reading: *"did the service come up cleanly?"* The
   assistant reads the session scrollback, which is redacted locally before it goes
   anywhere, and answers.
6. If it failed, ask *"why"*. You get a diagnosis and a proposed fix. Approve the fix, or
   dismiss it.

The loop closes without a ticket, and without you holding production credentials in a
second tool. The credentials stay in OpsPilot's connection store, encrypted by the
operating system; the AI sees redacted text and proposes strings.

## SRE — incident response

In this loop the read-only half can run without you clicking anything, and the destructive
half cannot.

1. The alert fires. Open the affected hosts as sessions — several at once, in the session
   tab bar.
2. Ask the obvious first question: *"what changed in the last hour on this host?"*
3. The read-only investigation runs. With **Auto-run safe commands** on, the
   `journalctl`, `ls`, `git log` and `systemctl status` style commands execute without a
   click, and you read results instead of approving steps. Auto-run ships off and applies
   to the whole workstation, so enable it only if this machine does not also reach
   production.
4. Correlate across hosts by asking about each session in turn. The assistant keeps the
   thread per session; no combined cross-host view is assembled.
5. The remediation arrives tiered. A read of a config file is
   <span class="tier tier-readonly">Read-only</span>. A service restart is
   <span class="tier tier-low">Low risk</span>. Anything matching your dangerous-pattern
   list, or anything the model flagged itself, is
   <span class="tier tier-high">High risk</span> and stops for a typed justification.

The justification is recorded with the command, on the card next to the exact command text
you ran. The card also states *why* the command was flagged: the matched pattern and the
profile it came from, or that the model flagged it itself.

## Network engineer

Switches and routers are the part of an estate least likely to have AI access at all, and
the place where the blast radius of a wrong line is largest.

1. Telnet or serial to the switch. Nothing is installed on the device.
2. Paste the configuration diff into the AI panel, or let the assistant read the
   scrollback from the session you are already in.
3. Ask what changed and what the blast radius is. This is the read-only half, and on a
   network device it is usually the whole job.
4. A proposed change to a core router will match your dangerous patterns and stop for
   justification. The classification is asymmetric: a pattern can push a command up to
   <span class="tier tier-high">High risk</span>, and nothing — not the model, not a
   prompt, not a setting — can pull one back down.

Telnet and RSH carry credentials and session content in clear text. That is a property of
the protocols, not of OpsPilot; see [connection types](../connections/connection-types.md)
before you use them outside a management network.

## DevOps — release validation

This loop uses the terminal, the object store and the AI in the same window.

1. Connect the release S3 bucket and the target hosts in the same OpsPilot window. The
   bucket is a saved connection like any other.
2. Verify the artefacts are present and that checksums match, before anything is rolled
   anywhere.
3. Roll out, watching logs across hosts in parallel sessions.
4. Ask for a comparison of staging and production configuration. This is a read across two
   sessions that you are already authorised for, not a new integration — and if the
   production connection has its AI badge off, the assistant cannot see it at all.

## Platform team — governing everyone else

Your job is the profiles, not the sessions.

Set up environments, groups, Command Safety profiles and Data Handling profiles, then let
each team work inside them. The engineer does not choose the guardrails; you do. A
realistic set looks like this:

| Group | Command Safety | Data Handling |
|---|---|---|
| Production | Strict, full pattern list, confirmation on | Strict — also scrubs hostnames and IPs |
| Staging | Moderate, full pattern list | Moderate |
| Lab | Permissive | Minimal |

Auto-run sits outside both profiles: it is one switch for the workstation, so it
cannot be set per group. Leave it off where the same machine reaches production.

The same assistant then behaves differently in each group without anyone changing a
setting when they switch tabs.

- Profiles resolve **connection → group → Default**, and they are *replacements*, not
  additions. A `Lab` profile can legitimately be less strict than `Default`.
- Because they are replacements, assigning a permissive profile to a production group
  silently loosens it. Keep that on your review list.

Profiles are configured per workstation and are not pushed to other workstations from
inside the product, so standardising them across a team is an organisational process —
documented, reviewed. [Administration & rollout](administration.md) covers what that means
in practice.

## See also

- [Administration & rollout](administration.md) — phasing this across a team
- [Command Safety profiles](../safety/command-safety-profiles.md) — patterns, confirmation
  and precedence
- [Approvals & auto-run](../safety/approvals.md) — what each tier asks of the engineer
- [Organising connections](../connections/organising.md) — groups and environment tags
