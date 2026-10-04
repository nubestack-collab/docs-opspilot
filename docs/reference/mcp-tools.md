# MCP tools

The complete tool surface OpsPilot exposes to an external AI Assistant. Six tools,
no more.

| Tool | Parameters | Returns | Redacted | Approval |
|---|---|---|---|---|
| `list_connections` | none | Saved connections this assistant may open: id, name, type, host, port. Locked connections, and in the free trial connections without AI, are left out | n/a | No — but gated |
| `list_sessions` | none | Open SSH sessions and Local Console tabs that have AI on, with host and a human-friendly label. A Local Console entry adds `kind: "local"`, its platform and its shell | n/a | No |
| `open_session` | `connectionId` (string) | A session id and a status once connected | n/a | No — pre-authorised |
| `get_recent_output` | `sessionId` (string) | The last 8,000 characters of terminal scrollback, from an SSH session or a Local Console tab | Yes | No |
| `read_file` | `sessionId` (string), `path` (string) | Remote file content over SFTP, from an SSH session only | Yes | No |
| `propose_command` | `sessionId`, `command`, `reason` (all string, required); `note` (string), `safe` (boolean), `dangerous` (boolean), all optional | The command's output after a human decision | n/a | **Yes** |

`list_connections` and `list_sessions` take no parameters at all.

Assistants are also given sequencing guidance when they connect: list sessions
before reading output or proposing a command, use `list_connections` and then
`open_session` only when the user asks about a saved connection that is not open,
and treat `propose_command` as approval-gated rather than as a way to run
something. Each tool's own description repeats the relevant part.

## The license check

Every tool checks the license before it does anything. AI Assistants need an active free
trial or subscription; while a paid license is paused (the clock, an identity check) or
not valid, they are paused or off as well. A refused call returns an error whose text
starts with "OpsPilot:" and gives the reason, for example "OpsPilot: AI assistant access
needs an active trial or subscription. The user can subscribe or activate a license in
OpsPilot > Settings > License." The connector itself keeps running, so the assistant
gets a reason rather than a dead connection, and a paying customer is never told to
subscribe.

Tools that take a `sessionId` also check that AI is on for that session under the
license. In the free trial, AI is on for 2 connections at a time, chosen by the user, so
`list_sessions` lists only sessions that have AI, and the others refuse. See [Free trial
and limits](../licensing/trial-and-limits.md).

## Read-only tools

`list_connections`, `list_sessions`, `get_recent_output` and `read_file` are all
declared read-only and non-destructive.

`get_recent_output` and `read_file` both pass their content through the same local
redaction step the in-app AI panel uses, on the same code path, before anything is
returned. Both also re-check the session's own AI flag before answering and refuse
if AI has been turned off for it, rather than trusting that a client only ever asks
about sessions it was just told about.

`get_recent_output` returns a window, not the whole buffer: the last 8,000
characters.

`read_file` works over SFTP, so it reads from SSH sessions only. Asked about a Local
Console tab, it refuses and tells the assistant to propose a command that prints the
file instead, which then goes through the approval flow.

`list_connections` returns only connections you have individually marked **Let AI
Assistant open this session**. It is also gated by the global switch: with **Allow
opening/reconnecting sessions** off, it returns an error and no list at all, so an
assistant cannot even enumerate saved connections. It also leaves out saved
connections that are locked (without a subscription, only your first 10 open) and, in
the free trial, connections that do not have one of the 2 AI places.

## `open_session`

`open_session` has no approval step. If the tool can be used at all, you have already
authorised it — twice:

1. **Allow opening/reconnecting sessions** in **Settings → AI Assistants → Session
   access**. Off by default. Without it, both `list_connections` and `open_session`
   return an error telling the assistant the setting is off.
2. **Let AI Assistant open this session** on the individual connection. Off by
   default, and re-checked against the connection itself, so a client holding a
   stale id from before you turned it off is refused.

The license is checked as well. A locked connection is refused with the reason. In the
free trial, a connection without one of the 2 AI places is refused, and so is one whose
tab would open with AI off because AI is already on in 2 other tabs; the assistant is
told that the user chooses in OpsPilot. Nothing opens in either case.

It blocks while the connect handshake runs, and gives up after 20 seconds. That is
deliberately much shorter than the proposal timeout, because there is no human to
wait for — only the connection itself.

## `propose_command` is the only tool with an approval gate

A proposal from an assistant enters exactly the same approval queue as a proposal
from the in-app AI panel, tagged so you can see it came from an assistant. It can
target an SSH session or a Local Console tab; a command for a Local Console tab runs on
your own workstation, in the shell `list_sessions` named. The tool call blocks until
one of these happens:

| Outcome | What the assistant is told |
|---|---|
| You approve it | The command's output |
| You dismiss it | "The human dismissed this proposal without running it" |
| Five minutes pass | "The human did not respond within 5 minutes — the proposal was not approved" |
| The assistant proposes again for the same session | The older call is superseded immediately |
| OpsPilot refuses it: the license no longer allows AI there, you turned AI off for the session, the session closed, or the window reloaded | The reason. The card in OpsPilot expires and can no longer be approved |

A proposal expires after five minutes. The connector's own HTTP timeouts are
disabled so that they cannot cut the call short before that. If the license takes AI
away from the session while an approved command is running, the command finishes but
its output is not returned to the assistant.

`safe` and `dangerous` are the model's own self-assessment, and they are the model's
only influence on the outcome. What they do depends on the Command Safety profile the
session resolves to:

- `safe` lets a command run without a click only when the profile's **Run commands
  without asking me** is **Only read-only commands** and the command does not count as
  dangerous. Under **Everything except dangerous ones**, any command that does not count
  as dangerous runs without a click, whatever `safe` says; under **Ask me every time**,
  nothing does.
- `dangerous` makes the command <span class="tier tier-high">High risk</span> while the
  profile's **What counts as a dangerous command** is **The AI's warning and my list**,
  the shipped choice. Under **Only my list**, the flag is shown on the card but not
  applied.
- A pattern match can add the dangerous classification; no pattern removes one.

A dangerous command always needs a click, plus a typed justification while **Make me type
a reason for dangerous commands** is on.

See [Approvals](../safety/approvals.md) and
[Execution boundary & risk tiers](../safety/risk-tiers.md).

## What the tool surface does not include

- **No tool writes a file.** `read_file` is the only file tool, and it is read-only.
- **No tool executes anything.** `propose_command` proposes. OpsPilot's own code is
  what runs a command, after a human decision or a rule you set in a profile.
- **No tool reads the credential store.** Passwords, keys and API keys are not
  reachable through any tool. `list_connections` returns names, types, hosts and
  ports only.
- **No tool changes a setting, a profile, a pattern list or the license.** Policy is
  set in the app, by you.

## Transport

| Property | Value |
|---|---|
| Bind address | `127.0.0.1` only |
| Default port | 8765 |
| Path | `/mcp` |
| Authentication | A bearer token on every request |
| Started | Only when you enable the connector in Settings |

The token may also be supplied as a `?token=` query parameter, because some clients'
custom-connector UI accepts only a bare URL with no header field. Binding to
loopback stops other machines, not other local processes running as the same user,
which is why the token is required as well.

The ChatGPT tunnel does not change this. The tunnel may forward only to OpsPilot's
own loopback endpoint, and any other destination is refused. No public listener is
opened. See [Remote and mobile access](../ai/remote-mobile.md).

## See also

- [AI Assistants](../ai/assistants.md) — connecting Claude Desktop, VS Code and ChatGPT
- [Remote and mobile access](../ai/remote-mobile.md) — the tunnel, and its limits
- [Approvals](../safety/approvals.md) — what happens to a proposal
- [Free trial and limits](../licensing/trial-and-limits.md) — what the trial allows
  assistants
