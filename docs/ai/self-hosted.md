# Self-hosted & enterprise

Two of the ten providers exist for organisations that cannot send terminal
content to a public API but do not want a model on every workstation either.
**Azure OpenAI** keeps inference inside your own cloud tenancy. **Custom /
Self-hosted** points at any OpenAI-compatible endpoint you already run and have
already had approved.

## Azure OpenAI

Azure OpenAI keeps inference inside your own Azure tenancy, under the
data-processing agreements your organisation has already signed with Microsoft.
For many regulated buyers the contractual work is therefore already done.

Two fields:

| Field | Shape |
|---|---|
| **API Key** | Your Azure API key — or leave it and use **Sign in with Microsoft** |
| **Endpoint + Deployment URL** | `https://RESOURCE.openai.azure.com/openai/deployments/DEPLOYMENT` |

The second field is the one that catches people out. It is not just the resource
endpoint: the **deployment name is part of the path**. One saved provider entry
therefore points at one deployment, and moving to a new deployment means editing
the URL, not a separate field. Copy both values from Azure AI Foundry →
**Keys and Endpoint**.

Sign-in uses a device authorisation grant through the Microsoft identity
platform, so it works against any Azure AD tenant without OpsPilot needing a
redirect URL registered anywhere.

## Custom / Self-hosted

One **Base URL**, one optional **API Key** — blank if your endpoint does not
require one. This provider is also the only one that accepts a **custom model
name**, because OpsPilot has no way to know what you have loaded; it ships no
model list for this entry at all.

What it covers:

- **vLLM** — a served open-weight model on your own GPUs
- **LM Studio** — a model on a workstation or a colleague's machine
- **llama.cpp server** — a lightweight local server
- **LocalAI** — a self-hosted drop-in API
- **an internal AI gateway** that already has your organisation's approval

That last one is often the real answer in a large organisation. If there is
already a sanctioned internal endpoint with logging, quota and a data-handling
sign-off attached, OpsPilot can use it and inherit the approval rather than
asking for a new one.

## OpenAI-compatible endpoints

Most of the catalogue speaks one request and response format: **OpenAI chat
completions**. It is what OpenAI, GitHub Copilot, Google Gemini, Groq, Mistral,
Azure OpenAI, OpenRouter and Custom / Self-hosted all use, and those entries
differ only in base URL, credential fields, model list and — for OpenRouter —
which endpoint the connection test checks. Anthropic Claude and Ollama use their
own native APIs instead.

The chat-completions shape has become the de facto interoperability format, so
if your endpoint speaks it, it already works. Enter the base URL under
**Custom / Self-hosted**, type the model name, test and save; there is nothing
to wait for.

## What does not change

A self-hosted or tenancy-local model does not relax anything. Redaction still
runs before the request leaves the workstation, using the Data Handling profile
resolved for the connection. The reply is still a proposal, still classified by
your Command Safety profile, and still requires a human approval event to become
a command.

!!! note "Self-hosted is not automatically air-gapped"
    A model inside your tenancy or on your network still means the workstation
    makes a network call. That is private or VPN-only operation. Only a model on
    the workstation itself — see [Offline with Ollama](offline-ollama.md) —
    closes the loop completely.

## See also

- [AI Providers](providers.md) — configuring, testing and switching providers
- [Offline with Ollama](offline-ollama.md) — zero-egress operation
- [AI provider matrix](../reference/provider-matrix.md) — every field and model
  per provider
- [Security model](../safety/security-model.md) — the controls a reviewer will
  ask about
