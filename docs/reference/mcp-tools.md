# MCP tools

The complete tool surface OpsPilot exposes to an external AI Assistant. Six tools,
no more.

| Tool | Parameters | Returns | Redacted | Approval |
|---|---|---|---|---|
| `list_connections` | none | Saved connections this assistant may open: id, name, type, host, port | n/a | No — but gated |
| `list_sessions` | none | Open SSH sessions, with host and a human-friendly label | n/a | No |
| `open_session` | `connectionId` (string) | A session id and a status once connected | n/a | No — pre-authorised |
| `get_recent_output` | `sessionId` (string) | The last 8,000 characters of terminal scrollback | Yes | No |
| `read_file` | `sessionId` (string), `path` (string) | Remote file content over SFTP | Yes | No |
| `propose_command` | `sessionId`, `command`, `reason` (all string, required); `note` (string), `safe` (boolean), `dangerous` (boolean), all optional | The command's output after a human decision | n/a | **Yes** |

`list_connections` and `list_sessions` take no parameters at all.

Assistants are also given sequencing guidance when they connect: list sessions
before reading output or proposing a command, use `list_connections` and then
`open_session` only when the user asks about a saved connection that is not open,
and treat `propose_command` as approval-gated rather than as a way to run
something. Each tool's own description repeats the relevant part.

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

`list_connections` returns only connections you have individually marked **Let AI
Assistant open this session**. It is also gated by the global switch: with **Allow
opening/reconnecting sessions** off, it returns an error and no list at all, so an
assistant cannot even enumerate saved connections.

## `open_session`

`open_session` has no approval step. If the tool can be used at all, you have already
authorised it — twice:

1. **Allow opening/reconnecting sessions** in **Settings → AI Assistants → Session
   access**. Off by default. Without it, both `list_connections` and `open_session`
   return an error telling the assistant the setting is off.
2. **Let AI Assistant open this session** on the individual connection. Off by
   default, and re-checked against the connection itself, so a client holding a
   stale id from before you turned it off is refused.

It blocks while the connect handshake runs, and gives up after 20 seconds. That is
deliberately much shorter than the proposal timeout, because there is no human to
wait for — only the connection itself.

## `propose_command` is the only tool with an approval gate

A proposal from an assistant enters exactly the same approval queue as a proposal
from the in-app AI panel, tagged so you can see it came from an assistant. The tool
call blocks until one of four things happens:

| Outcome | What the assistant is told |
|---|---|
| You approve it | The command's output |
| You dismiss it | "The human dismissed this proposal without running it" |
| Five minutes pass | "The human did not respond within 5 minutes — the proposal was not approved" |
| The assistant proposes again for the same session | The older call is superseded immediately |

A proposal expires after five minutes. The connector's own HTTP timeouts are
disabled so that they cannot cut the call short before that.

`safe` and `dangerous` are the model's own self-assessment, and they are the model's
only influence on the outcome. `safe` can let a command auto-run **only** if you have
turned on **Auto-run safe commands**, and only if it does not match a dangerous
pattern. `dangerous` requires you to write a justification before it can run. A
pattern match can add the dangerous classification; nothing removes one.

See [Approvals](../safety/approvals.md) and
[Execution boundary & risk tiers](../safety/risk-tiers.md).

## What the tool surface does not include

- **No tool writes a file.** `read_file` is the only file tool, and it is read-only.
- **No tool executes anything.** `propose_command` proposes. OpsPilot's own code is
  what runs a command, after a human decision.
- **No tool reads the credential store.** Passwords, keys and API keys are not
  reachable through any tool. `list_connections` returns names, types, hosts and
  ports only.
- **No tool changes a setting, a profile or a pattern list.** Policy is set in the
  app, by you.

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
- [Redaction categories](redaction-categories.md) — what is stripped before egress
