# AI provider matrix

The ten providers OpsPilot ships, how each one authenticates and what it asks you
for. Each is one card on **Settings → AI Providers**.

## Authentication, credentials and endpoints

| Provider | How you authenticate | Credential fields | Endpoint | Own model name |
|---|---|---|---|---|
| Anthropic Claude | Pasted API key | **API Key** | Not configurable | No |
| OpenAI / ChatGPT | Pasted API key | **API Key**, **Base URL (optional)** | Editable, optional | No |
| GitHub Copilot | Device authorisation grant (RFC 8628) | **Access Token (auto-filled on sign-in)** | Fixed | No |
| Google Gemini | OAuth 2.0 authorisation code with PKCE, or a pasted AI Studio key | **API Key** | Fixed | No |
| Groq | Pasted API key | **API Key** | Fixed | No |
| Mistral AI | Pasted API key | **API Key** | Fixed | No |
| Ollama (Local) | None needed | **Ollama Endpoint** | Editable | No |
| Azure OpenAI | Device authorisation grant, or a pasted Azure key | **API Key**, **Endpoint + Deployment URL** | You supply it | No |
| OpenRouter | Pasted API key | **API Key** | Fixed | No |
| Custom / Self-hosted | Pasted key, or none | **API Key (if required)**, **Base URL** | You supply it | Yes |

The five fixed endpoints:

| Provider | Endpoint |
|---|---|
| GitHub Copilot | `https://api.githubcopilot.com` |
| Google Gemini | `https://generativelanguage.googleapis.com/v1beta/openai` |
| Groq | `https://api.groq.com/openai/v1` |
| Mistral AI | `https://api.mistral.ai/v1` |
| OpenRouter | `https://openrouter.ai/api/v1` |

Anthropic's card has no base-URL field, so its endpoint is not configurable there.
OpenAI's base-URL field is optional and placeholders `https://api.openai.com/v1`.
Ollama's placeholders `http://localhost:11434`. Azure OpenAI's is your own resource
and deployment path; Custom's is your own endpoint.

**Custom / Self-hosted** is the only entry that takes a free-text **Model name**
field instead of a picker, and it ships with an empty model list for that reason.

Most of the catalogue is reached over the same OpenAI-compatible chat-completions
interface — eight of the ten entries, including Gemini through its own
OpenAI-compatibility path and OpenRouter, which is OpenAI-shaped by design. That is
why **Custom / Self-hosted** works at all: anything speaking that interface can be
pointed at, listed or not.

## Models offered per provider

| Provider | Models |
|---|---|
| Anthropic Claude | `claude-opus-4-8`, `claude-sonnet-4-6`, `claude-haiku-4-5-20251001`, `claude-3-5-haiku-20241022`, `claude-3-5-sonnet-20241022`. See the note below |
| OpenAI / ChatGPT | `gpt-4o`, `gpt-4o-mini`, `o1-preview`, `o1-mini`, `o3-mini`, `gpt-4-turbo`, `gpt-3.5-turbo` |
| GitHub Copilot | `gpt-4o`, `gpt-4o-mini`, `claude-3.5-sonnet`, `o1-mini` |
| Google Gemini | `gemini-2.0-flash-exp`, `gemini-1.5-pro`, `gemini-1.5-flash`, `gemini-1.5-flash-8b`, `gemini-2.0-flash-thinking-exp` |
| Groq | `llama-3.3-70b-versatile`, `llama-3.1-8b-instant`, `mixtral-8x7b-32768`, `gemma2-9b-it`, `llama-guard-3-8b` |
| Mistral AI | `mistral-large-latest`, `mistral-medium-latest`, `open-mixtral-8x7b`, `open-mistral-7b`, `codestral-latest`, `mistral-embed` |
| Ollama (Local) | `llama3.3`, `llama3.2`, `codellama`, `mistral`, `phi4`, `gemma2`, `qwen2.5-coder`, `deepseek-r1`, `starcoder2` |
| Azure OpenAI | `gpt-4o`, `gpt-4`, `gpt-4-turbo`, `gpt-35-turbo` |
| OpenRouter | Fetched live. Fallback list below |
| Custom / Self-hosted | None — you type the model name |

!!! note "Model lists go stale"
    These are the lists compiled into 0.1.0. The authoritative list is the one in the
    app's own model picker, and for OpenRouter it is fetched live from the provider.
    Treat this table as what shipped, not as what is currently available from each
    vendor.

!!! note "Pick the model in the app, and pull it before you test"
    Always select the model from the provider card's own picker rather than typing a
    name from a document — model lineups change, and the picker is what **Test**
    validates against.

    This matters most for **Ollama**. The picker's entries are the tags OpsPilot
    expects, `llama3.3` among them, and Ollama does not accept a typed model name.
    **Test** checks your selection against the models Ollama reports, so pull the
    exact tag you intend to select — a model you have not pulled fails the test
    rather than failing later during an incident.

### OpenRouter's fallback list

The OpenRouter picker fetches the current free-tier catalogue on load, with a
**Refresh live model list** button beside it. The list below is a safety net shown
instantly and used only if the live fetch fails; OpenRouter's free lineup changes
week to week, so take the live list as authoritative.

```text
meta-llama/llama-3.3-70b-instruct:free
deepseek/deepseek-r1:free
google/gemini-2.0-flash-exp:free
qwen/qwen-2.5-72b-instruct:free
mistralai/mistral-small-24b-instruct-2501:free
```

## Per-provider notes

**OpenRouter's Test verifies against an authenticated endpoint.** OpenRouter's model
catalogue is public and returns success for any key, including a fake one, so it
cannot validate anything. **Test** uses an endpoint that requires real
authentication and returns 401 on a bad key, which is what makes the result
meaningful.

**GitHub Copilot and Azure OpenAI use a device authorisation grant.** You are shown
a code, you approve it in a browser, and no redirect listener is needed. Copilot's
**API Key** field is filled in for you on sign-in; Azure's can be either the device
flow or a pasted key from Azure AI Foundry.

**Gemini offers two routes.** Full OAuth 2.0 authorisation code with PKCE, where the
browser opens and you click Allow, or a pasted AI Studio key for anyone who would
rather not sign in.

**Ollama needs no credential at all.** Its only field is the endpoint, and it is the
option that makes genuinely air-gapped operation possible. See [Offline with
Ollama](../ai/offline-ollama.md).

**Custom accepts an unlisted endpoint and an unlisted model.** Both the base URL and
the model name are left to you — LM Studio, vLLM, LocalAI and anything else
OpenAI-shaped. See [Self-hosted models](../ai/self-hosted.md).

Every card also has a **Console** link out to the provider's own key page, and a
**Test** button that verifies the credentials before you save.

## See also

- [AI providers](../ai/providers.md) — choosing and configuring one
- [Offline with Ollama](../ai/offline-ollama.md) — the air-gapped option
- [Self-hosted models](../ai/self-hosted.md) — the Custom entry in practice
- [What gets sent](../ai/what-gets-sent.md) — what reaches the provider, and what does not
