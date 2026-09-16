# Offline with Ollama

This is the configuration that makes OpsPilot viable in an air-gapped
environment. The model runs on the OpsPilot workstation, OpsPilot talks to it
over loopback, and nothing about the session reaches the internet because there
is nothing to reach it with.

## Setting it up

1. Install Ollama on the OpsPilot workstation.
2. Pull a model — OpsPilot's in-app guidance suggests `ollama pull llama3.3`.
3. In OpsPilot, choose **Ollama (Local)** and set the endpoint to
   `http://localhost:11434`.
4. Pick the model and test.

```bash
ollama pull llama3.3
```

The model picker for **Ollama (Local)** lists `llama3.3`, `llama3.2`,
`codellama`, `mistral`, `phi4`, `gemma2`, `qwen2.5-coder`, `deepseek-r1` and
`starcoder2`. Pull the one you intend to select — **Test** reads the models
Ollama actually has and tells you if the selected model is not among them, along
with what is.

!!! note "Pull the exact tag you are going to select"
    Pull a tag the picker lists — `llama3.3` rather than a shorter `llama3`, for
    instance. **Test** checks your selection against the models Ollama actually has,
    so a near-miss on the tag is reported as the model not being found.

The endpoint field is the only credential Ollama needs — there is no API key.
`http://localhost:11434` is Ollama's default; change it only if you have moved
Ollama's listener.

## Running with no egress

From that point, no terminal content, no command, no file and no metadata leaves
the machine. The redaction layer still runs, but with a local model there is no
egress for it to guard.

The interface ships every font and icon locally and references no external
resources, so the whole application functions with no route to the internet.
Settings, connections and profiles are local files; there is no account system,
no telemetry and no cloud backend to reach.

This is the only configuration the word *air-gapped* applies to. With any cloud
provider the workstation still reaches the internet, which is private or
VPN-only operation.

## What to expect from a local model

A smaller local model is less precise at diagnosis than a large cloud model. A
7B or 8B model will sometimes propose a command that is close but not right, and
sometimes miss a cause a larger model would find. That is the trade for running
with no egress at all.

For most operational work it is still useful:

- reading a stack trace and saying which frame matters
- explaining a configuration error
- proposing the next diagnostic command

Those tasks dominate real triage, and a local model handles them well. Keep a
larger cloud model for the hard ones: you can configure both and switch per
session from the provider dropdown at the top of the AI panel, so a workstation
that sometimes has connectivity is not tied to one choice.

## Hardware

The model size you can run is set by the memory and GPU on the workstation
rather than by OpsPilot. Size the workstation for the model you want, then point
OpsPilot at it.

## See also

- [AI Providers](providers.md) — configuring and testing any provider
- [Self-hosted & enterprise](self-hosted.md) — a shared internal endpoint
  instead of a model on every workstation
- [What gets sent](what-gets-sent.md) — what the redaction layer guards when
  there *is* egress
- [Network requirements](../reference/network-requirements.md) — what has to be
  reachable, and what does not
