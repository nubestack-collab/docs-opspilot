# AI

OpsPilot has two separate AI modes. **AI Providers** means you supply an API key
or a sign-in and the AI lives in OpsPilot's own panel. **AI Assistants** means an
app you already pay for — Claude Desktop, ChatGPT, VS Code Copilot Chat —
connects to OpsPilot over MCP and drives it from there.

| | AI Providers | AI Assistants |
|---|---|---|
| What it is | You supply an API key or sign-in | You connect an app you already pay for |
| Who drives | OpsPilot's own AI panel | Claude Desktop, ChatGPT, VS Code Copilot |
| Cost model | Pay per request, or free with a local model | Covered by your existing subscription |
| Offline capable | Yes, with Ollama or self-hosted | No — those apps are cloud services |
| Approval gate | Yes | Yes, identical |
| Redaction | Yes | Yes, same profiles |

Whichever mode a proposal arrives through, it enters the same approval queue,
gets the same risk tier, and its context passes through the same redaction layer
first.

Use **Providers** when you want the AI inside OpsPilot, or when you need a
deployment that never reaches the internet. Use **Assistants** when you would
rather work in a chat app you already have open, or drive the estate from your
phone. Both at once is fine — they are independent settings, not a choice.

## Pages in this section

- [AI Providers](providers.md) — the ten built-in providers, how to enter a
  credential, test it and switch model per session.
- [Offline with Ollama](offline-ollama.md) — the four steps that make OpsPilot
  work with no route to the internet at all, and what to expect from a local
  model.
- [Self-hosted & enterprise](self-hosted.md) — Azure OpenAI in your own tenancy,
  and any OpenAI-compatible endpoint you already run.
- [AI Assistants (MCP)](assistants.md) — the four assistants, the six tools they
  get, and how the safety model applies to them.
- [Remote & mobile operation](remote-mobile.md) — the ChatGPT Web/Work tunnel,
  and what works well from a phone.
- [Using the AI panel](using-the-ai-panel.md) — the day-to-day task: ask, read
  the proposal, then approve or dismiss.
- [What gets sent](what-gets-sent.md) — exactly what a provider or an assistant
  receives, and what never leaves the machine.

!!! note "Private or VPN-only is not air-gapped"
    With a cloud provider your *workstation* still reaches the internet, even
    though your servers do not. That is private or VPN-only operation. Only a
    local or self-hosted model closes the loop entirely.

## See also

- [How it works](../overview/how-it-works.md) — the eight-step loop end to end
- [Execution boundary & risk tiers](../safety/risk-tiers.md) — what happens to a
  proposal after the model produces it
- [AI provider matrix](../reference/provider-matrix.md) — every provider's
  fields, base URL, auth flow and models in one table
