# Troubleshooting

The eight failures that come up most often, what to check for each one, and in what order.

## Before you contact support

Gather these first. They are what makes an issue reproducible rather than a description.

- **The version** of the build you installed, taken from the download or installer
  you ran.
- **Reproduction steps** — what you clicked, in what order, and what happened instead.
- **Which mode.** An AI Provider you configured, or an AI Assistant connected over MCP.
  The two paths are separate and the fix rarely applies to both.
- **Which connection and group**, and therefore which Command Safety and Data Handling
  profiles resolved. Profiles resolve connection → group → Default.
- **The platform.** Windows, macOS or Linux. Several behaviours below differ per platform.

Support is included with an active subscription; see [Support](../about/support.md) for the
channel to use.

## SSH will not connect

Confirm reachability outside OpsPilot first. If this fails, nothing in OpsPilot will fix
it:

```bash
ssh user@host
```

Then, in order:

1. **Check the VPN is up.** A connection that worked yesterday and fails now, with no
   configuration change, is usually this.
2. **Check the host and port** on the saved connection, not on the one you meant to open.
3. **Check the key format is supported.** A key your own `ssh` client accepts is the right
   test; a key in a format OpsPilot cannot parse fails at connect time rather than at save
   time.
4. **Enter the passphrase.** A passphrase-protected private key needs its passphrase
   supplied in the connection's credential fields. There is no interactive prompt for it
   mid-connect.
5. **Wait for the timeout.** OpsPilot gives an SSH connection 20 seconds to complete before
   reporting failure, so a filtered port takes that long to report.

## The AI panel says no provider is active

Open **Settings → AI Providers**, configure one, and use **Test Connection**.

A provider that saves but fails the test is almost always one of two things: a wrong base
URL, or an expired or revoked key. Both report at test time; a provider is not active
because its fields are filled in.

A provider being active is separate from a connection being allowed to use it. AI access is
scoped per connection, and a connection with its AI badge off is invisible to every model
and every assistant. If the panel is inert on one session but works on another, check that
connection's **Enable AI** toggle before looking any further at the provider.

## Ollama is not responding

Confirm the service is running and the model is pulled, from a normal terminal:

```bash
ollama list
curl http://localhost:11434/api/tags
```

If `ollama list` shows the model and the `curl` returns a model list, the problem is the
endpoint OpsPilot is pointing at. Check it matches, **including the port**.

The test also fails if the model name configured in OpsPilot is not one of the models
Ollama has pulled — the same endpoint is used to check both, so "cannot reach Ollama" and
"that model is not here" are different messages. Read which one you got.

## Claude Desktop or VS Code does not see OpsPilot

The MCP connector must be enabled in OpsPilot **and** the assistant must be connected.
Both are required; either one alone looks identical from the assistant's side.

Both applications read their MCP configuration at startup, so after connecting them:

- **Claude Desktop** — quit and restart it. Not a new window: the process.
- **VS Code** — reload the window with `Ctrl+Shift+P` → **Developer: Reload Window**.

If it still does not appear:

1. **Reconnect after a token change.** The bridge reads OpsPilot's connector port and
   token when it starts. If you regenerated the token in Settings, the assistant needs to
   reconnect.
2. **Check OpsPilot is running.** The connector lives inside the application. Nothing
   answers when OpsPilot is closed.
3. **On Windows, beware two config files.** Microsoft Store (MSIX) installs of Claude
   Desktop read a virtualised copy of their configuration, and Claude Desktop's own
   **Edit Config** button can open the non-virtualised one instead. OpsPilot writes to the
   file the sandboxed application actually reads, so the file you are looking at by hand
   may not be the file in use.

## The ChatGPT tunnel will not start

Use **Run Doctor** on the tunnel row. Starting the tunnel runs the same check itself and
refuses to proceed if it fails, so Doctor's output is the first thing to read either way.

Then confirm, in order:

1. **The tunnel ID begins `tunnel_`.** OpsPilot rejects anything else, including a pasted
   URL.
2. **The runtime key is current.** It is stored encrypted and is only decrypted into the
   tunnel process; a rotated key has to be re-entered.
3. **The tunnel client binary path is correct.** Either the full path to
   `tunnel-client.exe`, or `tunnel-client` when it is on your `PATH`.

If the tunnel starts and then exits, OpsPilot reports the client's own output with secrets
stripped out of it. That output is the diagnostic to send to support.

!!! note "Localhost-only forwarding is enforced"
    The tunnel only ever forwards to OpsPilot on `127.0.0.1`. The destination is checked
    before the client is launched, and any other address is refused. No public listener is
    opened in this mode.

## Commands are not auto-running

This is expected unless you enabled auto-run. **Auto-run safe commands** lives in
**Settings → Security → Command Safety** and ships off; with it off, every proposal waits
for you, including
<span class="tier tier-readonly">Read-only</span> ones.

If auto-run is on and a command still waits, exactly one of two things happened:

- It was **not classified read-only** — it arrived as
  <span class="tier tier-low">Low risk</span> or
  <span class="tier tier-high">High risk</span>, which always require a human.
- It **matched a dangerous pattern** in the resolved Command Safety profile, which promotes
  it to <span class="tier tier-high">High risk</span>.

The card names which of the two applied. A pattern match names the matched pattern and the
profile it came from; a model self-assessment says it was flagged by the AI.

Auto-run never applies to anything above read-only, and no setting makes it do so.

## The redaction badge never appears

Either nothing matched, or the badge is switched off.

1. **Check that something was redactable.** The badge shows a 🔒 with a count only on turns
   where the redactor replaced something. A session with no credentials, keys, tokens or
   addresses in its scrollback produces no badge, correctly.
2. **Check `Show redaction badge`** in **Settings → Security**. It is a global, cosmetic
   setting.

!!! warning "Hiding the badge does not disable redaction"
    The badge is an on-screen indicator. Redaction happens on the single path every AI
    request takes, and this setting does not affect it. To confirm what a provider
    receives, turn the badge on and hover it — it names the categories that matched.

## RDP will not open

Confirm first that the target allows RDP at all and that your credentials are valid — the
same check you would make with any other client. After that, the answer depends on your
platform, because OpsPilot uses a different client on each.

| Platform | What OpsPilot launches |
|---|---|
| Windows | FreeRDP, bundled with the installer, embedded in an OpsPilot window |
| macOS | Microsoft's **Windows App**, handed a generated `.rdp` file |
| Linux | `xfreerdp` or `xfreerdp3`, as an external window |

- **Windows.** The FreeRDP client ships inside the installer, so there is nothing to
  install. If it is missing from the installation, OpsPilot falls back to the built-in
  Windows client instead of failing — a session that opens in a separate, differently
  behaved window is the symptom of that fallback.
- **macOS.** Windows App must be installed — search for it in the Mac App Store. Without
  it, the launch fails and OpsPilot says so. **Being prompted for your password by Windows
  App is expected**: OpsPilot never writes the password into the `.rdp` file it generates,
  the same rule it applies to every other credential.
- **Linux.** `xfreerdp` or `xfreerdp3` must be on your `PATH`; install it from your
  distribution's package manager. The launch also appends to `logs/xfreerdp.log` inside the
  application data directory, which is the first place to look when the window opens and
  immediately closes.

!!! note "RDP certificate handling"
    Certificate handling on RDP is provisional ahead of general availability. If your
    estate requires certificate validation on RDP, confirm the current behaviour with
    support before standardising on it.

## See also

- [Providers](../ai/providers.md) — configuring and testing a provider
- [Running offline with Ollama](../ai/offline-ollama.md) — endpoints, models and what
  offline actually means
- [AI Assistants](../ai/assistants.md) — connecting Claude Desktop and VS Code
- [Remote & mobile operation](../ai/remote-mobile.md) — the tunnel and its scope
