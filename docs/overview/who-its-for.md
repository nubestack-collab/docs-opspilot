# Who it is for

OpsPilot is aimed at engineers who operate infrastructure interactively — people
who spend their day in a terminal against machines other people depend on — and
at the security and platform teams who have to be comfortable with what those
engineers are running. Six groups get distinct value from it.

## Site reliability engineers

Incident response where the first ten minutes go on reading logs across four
hosts. Open a session per host, ask the AI panel what it sees, and it reads them
in parallel, correlates, and proposes the check you were about to type — with
secrets stripped before anything leaves the machine. The quick actions in the AI
panel (*Analyze Error*, *Explain Command*, *Review Config*, *Optimize*) cover the
questions you ask most often during an incident.

## DevOps and platform engineers

Release validation, rollout verification and the long tail of "why is staging
different from production". Because a
[Command Safety profile](../safety/command-safety-profiles.md) and a
[Data Handling profile](../safety/data-handling-profiles.md) attach per
connection or per group, the same assistant can be permissive in the lab and
strict in production without anyone remembering to change a setting first.

## Network engineers

Telnet and serial console access to switches, routers and firewalls that will
never run an agent, with AI help reading configuration diffs and interface state.
The connection types that matter here are the ones AI tooling normally ignores —
see [Connection types](../connections/connection-types.md).

## Developers

Deploy and test the code you just wrote against real infrastructure, without
waiting for a platform team ticket, and inside the guardrails the platform team
configured. OpsPilot does not try to replace the tool you write code in; it owns
the half that tool cannot reach — the environment the code has to run in.

## Security and compliance teams

A demonstrable boundary between an AI and a production shell, a redaction layer
that runs on one code path before egress, and a typed-justification gate on
destructive operations. What makes this reviewable is that the boundary is
architectural rather than configurable: there is no setting, provider or prompt
that turns it off. See [Security model](../safety/security-model.md).

## Regulated and disconnected environments

Banking, defence, healthcare, utilities, industrial control, and air-gapped
estates. This is the audience that has been told for years that AI assistance is
not available to them, because the hosts cannot reach a model and because handing
a model a shell was never going to pass review. OpsPilot addresses both halves —
and with a local model via [Ollama](../ai/offline-ollama.md), nothing leaves the
workstation at all.

## See also

- [Why OpsPilot](why-opspilot.md) — the properties these roles are relying on
- [Workflows by role](../operations/workflows-by-role.md) — what a working day
  looks like for each of them
- [Connection types](../connections/connection-types.md) — the ten types and what
  each one is for
