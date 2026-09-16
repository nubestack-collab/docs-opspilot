# How OpsPilot works

This page traces one complete turn — your question, the model's answer, the
command that runs — then sets out which of the two AI modes you are using and
where each kind of data is held. [Architecture](architecture.md) covers the
process separation and trust boundaries underneath it.

## The loop

1. **Connect.** OpsPilot opens an SSH, Telnet, RSH, Mosh, RDP, VNC, FTP, S3,
   serial or local session. Credentials come from the encrypted connection
   store.
2. **Ask.** Type a question in the AI panel, or use one of the quick
   actions — **Analyze Error**, **Explain Command**, **Review Config**,
   **Optimize**.
3. **OpsPilot gathers context.** Recent scrollback for that session is
   collected. Only that session's own buffer is used.
4. **OpsPilot redacts.** The text passes through the single redaction step,
   using the Data Handling profile resolved for that connection — connection,
   then group, then Default.
5. **The provider answers.** Only redacted text is sent. The reply is an
   explanation plus, usually, one proposed next command carrying the model's own
   assessment of it as safe or dangerous.
6. **OpsPilot classifies.** Your Command Safety profile is applied on top of
   that self-assessment. A pattern match can raise the tier; nothing can lower
   it.
7. **Approve.** <span class="tier tier-readonly">Read-only</span> may
   auto-run if you have switched that on. <span class="tier tier-low">Low
   risk</span> needs a click. <span class="tier tier-high">High risk</span> needs
   a typed reason and then a click.
8. **The command runs.** Its output is redacted as well, before it feeds into the
   next turn, so a multi-step investigation continues without you re-asking after
   every command.

Step 7 is the only way a proposal becomes a real keystroke on a real shell. That
holds for every AI path into OpsPilot, including an external assistant.

## The two AI modes

OpsPilot can either call a model you hold a key or sign-in for, or expose itself
to a chat application you already pay for. The two differ in cost, in whether
they can run offline, and in which application drives them. The safety behaviour
is identical.

| | AI Providers | AI Assistants |
|---|---|---|
| What it is | You supply an API key or sign-in | You connect an app you already pay for |
| Who drives | OpsPilot's own AI panel | Claude Desktop, ChatGPT, VS Code Copilot Chat |
| Cost model | Pay per request, or free with a local model | Covered by your existing subscription |
| Offline capable | Yes, with Ollama or self-hosted | No — those apps are cloud services |
| Approval gate | Yes | Yes, identical |
| Redaction | Yes | Yes, same profiles |

Use [AI Providers](../ai/providers.md) when you want the AI inside OpsPilot, or
when you need a fully offline deployment. Use
[AI Assistants](../ai/assistants.md) when you would rather work in a chat
application you already have open, or drive the estate from your phone. Running
both at once is supported.

## Where things live

| Data | Where | Notes |
|---|---|---|
| SSH passwords and private keys | Connection store | Encrypted via the OS credential store |
| OpenAI tunnel runtime key | Its own file | Encrypted via the OS credential store, decrypted only into the tunnel process |
| Connections, groups, environments | Application settings | Local to this machine |
| Redaction and safety profiles | Application settings | Local to this machine |
| Terminal scrollback | Memory only | Written out only by the explicit **Save terminal output** or **Print terminal output** actions |

Everything in that table stays on your workstation. OpsPilot has no cloud
backend, no account system and no sync service, so none of it is transmitted
anywhere.

## See also

- [Architecture](architecture.md) — the process separation and trust boundaries
  behind these steps
- [Execution boundary & risk tiers](../safety/risk-tiers.md) — steps 6 and 7 in
  full
- [What gets sent](../ai/what-gets-sent.md) — exactly what a provider receives
- [AI Providers](../ai/providers.md) — configuring the model that answers in
  step 5
